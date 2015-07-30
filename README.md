# mruby.vm.js

mruby compiled for the Web, ready to use version

# Motivation

I've been thinking about this for a while: [webruby](https://github.com/xxuejie/webruby) is going nowhere. The whole development/debugging flow is just a nightmare. There's no way this can stand up against competitors like [ClojureScript](https://www.youtube.com/watch?v=Ci4uviG8S0o) or [TypeScript](https://www.youtube.com/watch?v=_Z8L18MLpFk). It's just insane to get people build the whole LLVM first via emsdk, then mruby to finally get the single JS file so they can integrate to the browser environment.

So I created this repo to host ready to use webruby JS file containing the following 2 gems:

* [mruby-js](https://github.com/xxuejie/mruby-js) for providing JS FFI
* [webruby-multiline-parse](https://github.com/xxuejie/webruby-multiline-parse) for providing multi-line parser.

I will probably setup a cronjob to build webruby (maybe weekly?) using the latest emscripten with the latest webruby.

Notice this way, you won't be able to use the C source code (not until we can have fast dynamic linking in emscripten) in webruby. All you can try out is mruby APIs. However, you do have full JS at your disposal so that might alleviate the problems a little bit.

I understand, that this might still be inferior to what Opal can provide these days. But I just believe it might be nice to provide this project just in case someone is interested. Besides, it won't take too much efforts from me(at least for now).

So these days, my recomendation is as following:

1. If your stack requires mruby, and having a Web-based IDE will definitely help. Then feel free to use webruby as it is, or use webruby as a motivation to setup your own build process.
2. If you are instead in a plug-n-play version of mruby in the browser, this repo will give you exactly what you need.
