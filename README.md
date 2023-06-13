# ruby-wasm-testom

Following the steps from [the ruby.wasm example](https://github.com/ruby/ruby.wasm#quick-example-how-to-package-your-ruby-application-as-a-wasi-application) on how to pack a Ruby application as a WASI application, using the JS package it doesn't work.

The steps to reproduce are as follow: 

```bash
# Download a prebuilt Ruby release
$ curl -LO https://github.com/ruby/ruby.wasm/releases/latest/download/ruby-3_2-wasm32-unknown-wasi-full-js.tar.gz
$ tar xfz ruby-3_2-wasm32-unknown-wasi-full-js.tar.gz

# Extract ruby binary not to pack itself
$ mv 3_2-wasm32-unknown-wasi-full-js/usr/local/bin/ruby ruby.wasm

# Put your app code
$ mkdir src
$ echo "puts 'Hello'" > src/test.rb

# Pack the whole directory under /usr and your app dir
$ wasi-vfs pack ruby.wasm --mapdir /src::./src --mapdir /usr::./3_2-wasm32-unknown-wasi-full-js/usr -o pr.wasm
```

We then created a simple html file, as described [here](https://github.com/ruby/ruby.wasm/tree/main/packages/npm-packages/ruby-3_2-wasm-wasi#quick-start-for-browser) and we have
run our packed wasm, as it can be seen in the `index.html` file in this repo.

In order for this to work, we also had to modify the `browser.umd.js` file not to call the [`_initialize` function](https://github.com/ruby/ruby.wasm/blob/394841d142fabc2287e7f918a605c7009e545846/packages/npm-packages/ruby-wasm-wasi/src/browser.ts#L97) as in the released binary it did not exist.

Once run, this small example seems to contradict the idea that our wasm file is now packed with a virtual filesystem that should contain our application code. In fact, we run the following ruby code in the
browser:

```ruby 
puts Dir.entries("/src")
puts File.read("/src/test.rb")
```

the first line just prints `./` and the second line fails.
