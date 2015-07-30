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

# APIs

To use the script, we need to either use a `script` tag in the browser, or `require` in node.js. It will pollute the global namespace, no CMD or AMD module is supported for now.

First, we need to create a webruby object:

```
> var webruby = new WEBRUBY();
```

Then you can run Ruby source code directly:

```
> webruby.run_source('5.times do puts "I\'m in Ruby!" end')
I'm in Ruby!
I'm in Ruby!
I'm in Ruby!
I'm in Ruby!
I'm in Ruby!
0
```

You can also determine if you've only received part of the source code:

```
> webruby.multiline_run_source("5.times do")
2
> webruby.multiline_run_source("5.times do puts 'multiline mode'")
2
> webruby.multiline_run_source("5.times do puts 'multiline mode' end")
irep 0x535060 nregs=3 nlocals=1 pools=0 syms=1 reps=1
      000 OP_LOADI	R1	5	
      001 OP_LAMBDA	R2	I(+1)	2
      002 OP_SENDB	R1	:times	0
      003 OP_STOP

irep 0x5350b0 nregs=4 nlocals=1 pools=1 syms=1 reps=0
      000 OP_LOADSELF	R1		
      001 OP_STRING	R2	L(0)	; "multiline mode"
      002 OP_SEND	R1	:puts	1
      003 OP_RETURN	R1	return

multiline mode
multiline mode
multiline mode
multiline mode
multiline mode
0
```

Or you can also use mruby bytecode directly:

```
$ cat test.rb
5.times do puts "This is bytecode mode!" end
$ mrbc test.rb
$ node
> require("./webruby.js");
> var fs = require('fs');
> var buf = fs.readFileSync("./test.mrb");
> buf
<Buffer 52 49 54 45 30 30 30 33 42 24 00 00 00 a0 4d 41 54 5a 30 30 30 30 49 52 45 50 00 00 00 82 30 30 30 30 00 00 00 32 00 01 00 03 00 01 00 00 00 04 00 c0 ... >
> var webruby = new WEBRUBY();
> webruby.run_bytecode(buf)
This is bytecode mode!
This is bytecode mode!
This is bytecode mode!
This is bytecode mode!
This is bytecode mode!
0
```

Finally, you close the object to cleanup resources:

```
webruby.close();
```
