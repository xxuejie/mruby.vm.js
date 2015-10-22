# mruby.vm.js

mruby compiled for the Web, ready to use version

# Motivation

While I do think you can only experience the power of webruby if you build it from scratch yourself, let's face it, some people's use case might be better satisfied if a simple JS file can be dragged in and run Ruby right way.

This repo is created to fulfill that goal: a ready to use webruby.js file that you can just download and use!

For now, the webruby.js packed here is built with following gems:

* [mruby-js](https://github.com/xxuejie/mruby-js) for providing JS FFI
* [webruby-multiline-parse](https://github.com/xxuejie/webruby-multiline-parse) for providing multi-line parser.

I will probably setup a cronjob to build webruby (maybe weekly?) using the latest emscripten with the latest webruby.

Notice this way, you won't be able to use the C source code (not until we can have fast dynamic linking in emscripten) in webruby. All you can try out is mruby APIs. However, you do have full JS at your disposal so that might alleviate the problems a little bit.

I understand, that this might still be inferior to what Opal can provide these days. But I just believe it might be nice to provide this project just in case someone is interested. Besides, it won't take too much efforts from me(at least for now).

In the future, when there's more gems to choose, I might provide multiple version of webruby.js file for different case. And the ultimate goal is to provide something like [AWS SDK for JavaScript](https://sdk.amazonaws.com/builder/js/), where you can just click and choose what gem you want, we will build that for you. But that's just a nice vision and might be overkilled for now.

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
