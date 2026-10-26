{:title "Kobo Deluxe in the Browser"
 :layout :post
 :author "Jeff Terrell"
 :tags ["games" "webassembly" "sdl"]
 :toc false}

I spent many hours in college playing what I consider to be one of the best 2d
arcade shooters out there: [Kobo Deluxe](https://github.com/olofson/kobodeluxe),
by [David Olofson](http://olofson.net/kobodl/). As an experiment, I asked Claude
Opus to port it to WASM so I could [play it in my browser](/kobo/).

<!--more-->

The game was written in C/C++ against SDL 1.2, so getting it onto the web meant
compiling it to WebAssembly with [Emscripten](https://emscripten.org/). The
game's own code is unchanged. It runs on
[sdl12-compat](https://github.com/libsdl-org/sdl12-compat), which implements the
SDL 1.2 API on top of SDL2, and SDL2 handles graphics, sound and input in the
browser.

A few notes:

- Click the game to start it, because browsers won't play sound until you do.
- Arrow keys move, Ctrl or Shift fires, and Esc brings up the menu.
- Your scores and settings are saved in your browser.

Kobo Deluxe is free software under the GPL. All credit for the game goes to
David Olofson and the other Kobo contributors. The source for this web version,
including the build script, is at
[github.com/kyptin/kobodeluxe-web](https://github.com/kyptin/kobodeluxe-web).
