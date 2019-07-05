{:title "Reducing the conceptual load of reduce"
 :layout :post
 :author "Jeff Terrell"
 :tags ["analysis" "clojure" "code" "legibility"]
 :toc false}

<em>Abstract: calls to </em>`reduce`<em> are difficult to understand. An example </em>`reduce`<em> in a code sample is analyzed and discussed to explore the reasons. An alternative version without the </em>`reduce`<em> is offered and compared.</em>

I'm really keen on legible code. Apart from correctness, I think legibility is the most important thing for software engineering, where the main cost is in communicating things between people. The trick is that "legible" is neither objective nor quantifiable, but that's actually how it should be: we're dealing with humans, here.

In my quest for legibility, I do code reviews. I recently saw the following code (in Clojure). I think it's not quite as legible as it could be.

<!--more-->

```clojure
(let [env-vars ["PORT" "DATOMIC_URI" "ASSET_ROOT"]
      specified (reduce
                  (fn [acc var-name]
                    (if-let [val (System/getenv var-name)]
                      (assoc acc (env-var->keyword var-name) val)
                      acc))
                  {}
                  env-vars)]
  ...)
```

What is this doing? It's taking a list of possible environment variable names (like `"ASSET_ROOT"`), and, if the variable has a non-empty value, converting the name to a Clojure-friendly keyword (like `:asset-root`) and adding the keyword and the corresponding value to a map. In other words, it goes from:

```bash
export PORT="8080"
export ASSET_ROOT="assets/"
```

To:

```clojure
{:port "8080"
 :asset-root "assets/"}
```

How legible is this code? Well, it's not too bad. We have:

---

| Element | Analysis | Score |
| ------- | -------- | ----- |
| `let` | Standard Clojure and LISP-y fare. Somewhat involved relative to a plain old function, but one of the first things any LISP-er learns. | 1 |
| `env-var->keyword` | A user-defined function. Well-named, conveys meaning well. | 1 |
| `assoc` | Simple function that almost all Clojurians have in their vocabulary. | 1 |
| `if-let` | Not a widely-known core function, and a little complex to understand. (It takes a while to grok that the binding is available only in the consequent expression, not the alternate expression. And I still don't know `if-let` well enough to know offhand whether I can use multiple bindings, and, if so, what the semantics for the `if` part are.) So: somewhat complex. | 5 |
| `reduce` | See below. | 9 |
| `fn` | Basic Clojure stuff. | 1 |

---

Why do I give `reduce` a score of 9? I know `reduce` well. Heck, we first met 12 years ago in college. Even so, I have to slow down when I see a `reduce`. What a given call to `reduce` does doesn't spring into my mind like `assoc`, `let`, or `if`.

I think the reason is that, even though I can often understand the reducing function without too much effort, it is still difficult to understand the overall effect of the reduction. In other words, I have a (hopefully simple) function, but I have to understand the emergent effect of the function as it is used by `reduce`. I have to hold the function in my mind—including all its conditional branches—as I walk through the operation of `reduce`, until I eventually see how the complete call to `reduce` shapes the output. Holding both the function and `reduce` in my mind can be challenging.

Another reason why `reduce` tends to be less legible is that it entangles multiple transformations into a single function. Clojurians would say that it "complects" multiple concerns. Isolating and disentangling the individual transformation steps can result in more legible code, as the example below will show.

The total weight in my totally subjective complexity score is 18. Can we do better?

Thankfully, with a good understanding of Clojure core functions, we have several good alternatives. Here's one that I consider pretty legible:

```clojure
(let [env-vars ["PORT" "DATOMIC_URI" "ASSET_ROOT"]
      specified (->> env-vars
                     (map (juxt env-var->keyword #(System/getenv %)))
                     (remove (comp nil? second))
                     (into {}))]
  ...)
```

How legible is the alternative?

---

| Element | Analysis | Score |
| ------- | -------- | ----- |
| `let` | Same as above. | 1 |
| `->>` | A threading macro. I think threading macros take a little work to understand. | 2 |
| `map` | A simple and widely understood Clojure and LISP tool. | 1 |
| `juxt` | This higher-order function takes a little work to understand, and it isn't super common. | 3 |
| `env-var->keyword` | Same as above. | 1 |
| `#()` | A function literal. Basic Clojure stuff. (It's a shame we can't just use `System/getenv` as a function.) | 1 |
| `remove` | The dual of the ubiquitous `filter` function. Not quite as common as its brother, but still, pretty simple. | 1 |
| `comp` | You have to be fairly comfortable with functions to be comfortable with this. One tricky bit is that the order in which the given functions are called is the reverse of the order in the parameter list. | 5 |
| `into` | Pretty basic Clojure function, and easy to understand, though not one of the first things people usually learn. Although this is made a little bit trickier because you have to know that you put pairs of values into a map. | 3 |

---

The total complexity of my alternative: 18.

It's a wash! Both alternatives are 18. So maybe `reduce` is just as good in this case. Nevertheless, I think the exercise of analyzing legibility is worthwhile, and I still consider the `reduce`-less alternative simpler for those comfortable with higher-order functions.

Addendum: Our very own Brad Dollard prefers the `reduce` version. He says that he traces the execution of the seed and the first element through the reducing function, and at that point it's pretty easy for him to see the pattern. In any case, I think all would agree that this is very much a matter of taste and style. Nevertheless, I think it's a worthwhile discussion to have, so long as nobody's feelings get hurt. :-)

<hr/>

<em>Edit 2015-07-09 at 3:55pm EDT:</em> <a href="https://twitter.com/gtrakGT">@gtrakGT</a> suggests that a `for`-based version, like the one below, might be even more legible. I'm inclined to agree!

```clojure
(let [env-vars ["PORT" "DATOMIC_URI" "ASSET_ROOT"]
      specified (into {} (for [var-name env-vars
                               :let [val (System/getenv var-name)]
                               :when val]
                           [(env-var->keyword var-name) val]))]
  ...)
```
