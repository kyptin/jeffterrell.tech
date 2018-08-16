{:title "Advanced tips for the bash command-line"
 :layout :post
 :author "Jeff Terrell"
 :tags ["bash" "screencast" "command line" "youtube"]
 :toc false}

I just published [a screencast on advanced command-line tips](https://youtu.be/gU5nFonVEh0) from a short talk I gave last year. I try to keep my screencasts brisk to avoid wasting anybody's time, but I also understand some would prefer to have the content in a more skimmable format; hence this post.

<!-more->

Note that this is more an info-dump than a tutorial, so if any of these topics are unfamiliar, I suggest watching how I use them in the video.

My advanced bash/CLI tips:

- history expansion with `!!`, which is a reference to the previous command
- `for` loops, to run a command for each of many items
- process substitution with `<()`, useful to have more than one input stream to a single process
- the `fc` or 'fix command' utility, where you can edit multiple previous commands in your editor of choice
- making a change to the previous command with `^old^new` or globally with `!!:gs/old/new/`
- the `yank-last-arg` readline command, typically bound to `M-.`, which inserts the last word of the previous command at your cursor position
