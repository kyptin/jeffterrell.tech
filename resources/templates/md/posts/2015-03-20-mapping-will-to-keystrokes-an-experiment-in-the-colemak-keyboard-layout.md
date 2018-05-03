{:title "Mapping will to keystrokes: an experiment in the Colemak keyboard layout"
 :layout :post
 :author "Jeff Terrell"
 :tags ["cognition" "colemak"]
 :toc false}

About 3 weeks ago, I switched to the <a href="http://colemak.com/">Colemak keyboard layout</a>. It's much more efficient than the standard Qwerty layout but not as alien as the more well-known alternative, Dvorak.

It's been challenging. I was typing around 115 words per minute using Qwerty, and I had to toss that out and start over from zero. Ouch. The first week was especially frustrating. Things took much longer to type than my mind was expecting, and I often lost my train of thought in the process of getting it out of my head.

At this point, I've recovered about a third to a half of my speed. It's no longer very painful. I'm getting there.

However, there's one area in my muscle memory that is resisting the advances of the new Colemak regime: <a href="http://www.vim.org/about.php">vim</a>. Vim is frustrating all over again, much like when I was first learning it in college.

<!-more->

In thinking about where the disconnect lies, I realized something. <em>I no longer mediate text editing actions through the letters that represent them.</em>

When I learned vim, I had to memorize some vocabulary in the vim “language”. For example, I memorized that `j` means “move down a line”. Then, through practice and repetition, I reversed the mapping, so that when I wanted to move down a line, I remembered that the corresponding command is `j`, which in turn is executed by my right index finger pressing the `j` key. The `j` letter served as a bridge between a text editing command (“move down”) and a way to execute the command (“press this key”).

Eventually, though, the letter “bridge” drops away. Desire fuses with execution into an action that becomes baked in to my muscle memory, the same way martial artists bake in a set of reflexes to physical situations. The mediation layer disappears. I'm no longer speaking in a language of letters, but rather more complex text editing commands.

In this more mature state, text editing actions don't rely on the language of letters for their expression. They instead rely on the language of keystrokes—the same language that the language of letters itself rests on.

It's worth noting that I can still access the letter in my analytical mind. If I'm teaching vim to someone, it takes no effort to remember that the deletion operator is `d`, or that the end-of-line motion is `$`. The difference is that I don't have to access the letter when I'm executing the desire.

I would argue that this is a feature not a bug. It's good to be able to collapse a layer of mediation and train your muscle memory. However, that feature works against me when I switch the mediation layer, because muscle memory is a function of mediation layer. As an analogy, consider learning a new spoken language. Even though one can express the same meaning in the new language, one must learn new a new mediation layer, or vocabulary. All the knowledge of the old language doesn't help, except in cases where the mediation happens to be the same in both cases, which are called cognates. So it is with a new keyboard layout, which is just a mediation layer.

I'll also note that it's not just vim. I use some basic emacs bindings (like `C-F`, `M-B`, and `M-D`) often because they are accessible in most of the typing areas I encounter on my Mac. The same effect even happens on the command line. My mind no longer thinks of the `ls` command as “the letter `l` followed by the letter `s`”. Instead, I have a desire that might be verbalized something like “see what's here”, that comes out of my fingers as a pair of keystrokes. The `cd`, `cp`, and even `vim` commands have also become something more basic than the letters that comprise them. Again, though this works against me when I swap the mediation layer, it is a feature [1] that speeds me up. Vim is a particularly egregious example in this case, though, because the basic directional motions (`h`, `j`, `k`, and `l`) are typed with my right index finger in Colemak.

In conclusion, I'm sticking with Colemak. I believe the promise that I will eventually be faster with it than I was with Qwerty. Plus, the exercise of retraining my muscle memory keeps me limber. Most importantly, I'm enjoying the exploration of my internal mental processes, because it's illuminating the mechanisms that I use for executing my will efficiently.

[1] This feature is called “<a href="http://en.wikipedia.org/wiki/Chunking_(psychology)">chunking</a>”. The mind treats a sequence of things as a single thing, for more efficient operation. That's how some people can read words with jumbled letters, like <a href="http://coursework.mnsfld.edu/psy1101loher/chunking.htm">this</a>. Or how tactics comprising single chess moves can congeal, in the mind of an expert, into larger strategies.
