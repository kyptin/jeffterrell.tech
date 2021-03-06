{:title "Lazy reduce in Clojure"
 :layout :post
 :author "Jeff Terrell"
 :tags ["clojure"]
 :toc false}

We’re big fans of Clojure at Altometrics. It really gets out of the way and lets us get stuff done without a lot of fuss. Like many functional programming languages, Clojure features lazy sequences, which are especially useful if a given sequence is too big to fit into memory. (I’ll call such sequences “large” for the purposes of this post.)

I recently wrote the following Clojure function to lazily `reduce` a large sequence. I couldn’t find a Clojure function that does this, so I figured it would be worth sharing. The discussion follows, but first, here’s the code:

<!--more-->

```clojure
(defn sidelong-reduce
  "Do a lazy reduce, returning the original (lazy) sequence,
  and passing the final accumulator value to `store-result!`
  when the sequence is fully realized."
  [seq f accum store-result!]
  (lazy-seq
    (if (empty? seq)
      (do (store-result! accum) nil)
      (let [[fst & rst] seq]
        (cons fst
              (sidelong-reduce rst f (f accum fst) store-result!))))))
```

## Eager versus lazy functions

Those familiar with Clojure know that functions returning sequences can be classified by whether they return a lazy sequence or not. We call the former "lazy functions" and the latter "eager functions". Eager functions consume the entire sequence without delay, as programmers coming from non-functional languages often expect. Lazy functions, on the other hand, return a lazy sequence, whose items are not calculated (or "realized") until they are needed.

Most functions naturally "want" to be one or the other. For example, the `range` function, which returns a sequence of (typically consecutive) integers, is lazy: it doesn't need to calculate the entire sequence when it's first called, so it doesn't. It returns a lazy sequence, which is essentially the first item and a promise to compute the next one whenever it's needed. Or consider `filter`, which returns a sequence of only the items in the input sequence that pass a given predicate. It doesn't need to do the full scan up-front. It can simply find the first match (if any), and return a promise to find the next match whenever it's needed—which is exactly what it does. It's lazy.

On the other hand, some functions "want" to be eager. For example, `reverse` has to go all the way to the end of the sequence in order to return the first item, so it may as well return the whole reversed sequence at once—there's no benefit here in returning a lazy sequence. Also, although vectors are considered sequences, there is no such thing as a lazy vector, so any function returning a vector (like `vec`, `mapv`, or `filterv`) is de facto eager.

One very good reason to use lazy sequences is that, if you use them “correctly” (a qualification we'll explore below), they allow you to process "large" sequences that cannot fit into memory. In fact, you can even process [infinite sequences](https://mitpress.mit.edu/sicp/full-text/sicp/book/node71.html)!

## Pitfalls of large lazy sequences

What do I mean when I talk about using a large lazy sequence "correctly"? You have to be careful that you don't accidentally force it to all be in memory. For example, simply calling `reverse` will pull the entire sequence into memory before returning anything, because it has to traverse the entire sequence to get the first item of the returned sequence. Worse yet, the items pulled into memory cannot be garbage collected because they might be needed later. If your sequence is "large", using the built-in reverse function will break your program; you must instead do something fancy, like a divide-and-conquer algorithm to only reverse a section at a time.

The term Clojurists use to describe this problem is "retaining the head" of the sequence. As an example of head-retaining code, consider this snippet:

```clojure
(def a (range))
(def b (count a))
```

If you try running that, it will eventually grind your JVM to a halt because you'll run out of memory. It's trying to exhaust an infinite sequence while retaining a reference (`a`) to the head of the sequence. The `a` reference prevents the garbage collector from reclaiming any items once you pass by them. In contrast, consider this code:

```clojure
(def b (count (range)))
```


If you try running that, it will never finish (because the sequence is infinite), but it will not run out of memory because the garbage collector is reclaiming items as fast as they are being counted.

So, in summary: don't retain the head.

## Motivation for `sidelong-reduce`

…Except sometimes you really want to retain the head. For example, let's say you have a large sequence of numbers, and you want to both `count` and `sum` them. Remember, if you do these operations in succession, you'll be retaining the head, because after the first operation you've fully realized the sequence but still need to keep the head around for the second operation. That's why the following naive solution will throw an `OutOfMemoryError`:

```clojure
(defn naive-count-and-sum
  [nums]
  (vector
    (count nums)
    (reduce + 0 nums)))
```

What are your options? You could rig up some complicated parallel processing jiggery and hope that the two reductions don't get too out of sync. Or you could mash the two reductions together into a single reduction, but doing so entangles the code together in a way that you'd like to avoid in your quest for [simplicity and separated concerns](https://github.com/matthiasn/talk-transcripts/blob/master/Hickey_Rich/SimpleMadeEasy.md). What to do? (Note: I haven't yet understood reducers; if they would solve this problem without entangling the concerns, do let me know!)

There, at long last, is the motivation for the `sidelong-reduce` function above. With `sidelong-reduce`, you only perform the reduction as the sequence is realized by some external process. This allows you to wrap a lazy sequence in a sidelong reduction and _get the exact same lazy sequence out again_, with the result of the reduction published to the given side channel when it's ready. Thus, you can compose an arbitrary number of reductions together (although you still must be careful not to accidentally retain the head).

## Example: composing lazy reductions

For example, to both count and sum a large sequence called `nums`, you can do this:

```clojure
(defn sidelong-count
  [nums result-atom]
  (letfn [(step [accum item]
            (inc accum))]
    (sidelong-reduce nums step 0 #(reset! result-atom %))))

(defn sidelong-sum
  [nums result-atom]
  (sidelong-reduce nums + 0 #(reset! result-atom %)))

(defn sidelong-count-and-sum
  [nums]
  (let [count-atom (atom nil)
        sum-atom (atom nil)]
    (-> nums
      (sidelong-count count-atom)
      (sidelong-sum sum-atom)
      dorun)
    [@count-atom @sum-atom]))
```

I admit, using `sidelong-reduce` is a little more complicated than just using `reduce`. I think this is the price to pay for having an approach that uses a side channel for results, which I think is necessary given the nature of the problem. However, if you find a more elegant solution, do tell! We might even feature your solution (crediting you, of course) in a followup post.

Note that the `dorun` is necessary to force the realization of the entire sequence, which is a requirement for `sidelong-reduce` to ever actually publish a result. (Don't use `doall`, which intentionally retains the head of the sequence.) In many real-world cases, such as writing a large sequence to disk, you will have some other "driver" forcing the realization of the sequence and won't need to use `dorun`.

## Try it out

If you're curious, try it yourself. Try starting a Clojure REPL, pasting in the `sidelong-reduce` defn and the above defns, and call it:

```clojure
(sidelong-count-and-sum (range 100000000))  ;; 100M items
[100000000 4999999950000000]
```

I did this in a Clojure REPL process with 512 MB of available memory.<sup>1</sup> The sidelong version returned the correct answer in about a minute. The naive, head-retaining version threw an `OutOfMemoryError`.

<sup>1</sup> Assuming you've using Leiningen before, you should be able to use this or a similar incantation to start a limited-memory Clojure REPL:

```bash
java -Xmx512m -jar \
  ~/.m2/repository/org/clojure/clojure/1.6.0/clojure-1.6.0.jar
```

<em>See a problem? Have a refinement or alternative to suggest? Let me know in the comments below</em>. Or join the conversation on Twitter:

<blockquote class="twitter-tweet" lang="en"><p>Lazy Reduce in <a href="https://twitter.com/hashtag/Clojure?src=hash">#Clojure</a> by <a href="https://twitter.com/kyptin">@kyptin</a> <a href="http://t.co/TVmldcYpmk">http://t.co/TVmldcYpmk</a></p>&mdash; Altometrics, Inc. (@Altometrics) <a href="https://twitter.com/Altometrics/status/570307254085423105">February 24, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
