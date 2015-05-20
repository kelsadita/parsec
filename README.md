> ES6 generator-based CLI options parser.

![](https://img.shields.io/badge/---Parsec-05B3E1.svg?style=flat-square)
![](https://img.shields.io/badge/---ES6-FF2C77.svg?style=flat-square)
![](https://img.shields.io/badge/---MIT-36DF91.svg?style=flat-square)

<a name="parsec"></a>

<p align="center">
<a href="https://github.com/bucaran/parsec/blob/master/README.md">
<img width="95%" src="https://cloud.githubusercontent.com/assets/8317250/7609248/1735d894-f9ac-11e4-9f80-eb0483533355.png">
</a>
</p>

<p align="center">
<b><a href="#install">Install</a></b>
|
<b><a href="#synopsis">Synopsis</a></b>
|
<b><a href="#usage">Usage</a></b>
|
<b><a href="#example">Example</a></b>
|
<b><a href="#development">Development</a></b>
|
<b><a href="#about">About</a></b>
</p>


# Install [![Build Status][TravisLogo]][Travis]

```sh
npm install parsec
```

# Synopsis

_Parsec_ is a hand-crafted CLI options parser using ES6 generators in ~150 LOC.

```js
Parsec.parse(process.argv) // -T850
  .options(["T", "terminator"], { default: 800 })
  .options("model", { default: 101 })
  .options("name", { default: "Schwa" })
```
```json
{
  "T": "850",
  "terminator": "850",
  "model": "101",
  "m": "101",
  "name": "Schwa",
  "n": "Schwa"
}
```

# Usage

_Parsec_ automatically slices arguments starting from index `2`, but you can specify the `sliceIndex` via the second argument to `parse`.

## Syntax
```js
Parsec.parse(argv[, {
  sliceIndex = 2,
  operandsKey = "_",
  noFlags = true
}])
  .options("option string")
  .options([aliases], { default })
  ...

```

_Parsec_ uses the _first_ letter of an option string as an alias:

```js
Parsec.parse(["-tla"], { sliceIndex: 0 })
  .options("three")
  .options("letter")
  .options("abbreviation")
```
```json
{
  "t": true,
  "l": true,
  "a": true,
  "three": true,
  "letter": true,
  "abbreviation": true
}
```

Pass an array of aliases, or specify default values via `{ default: value }`

```js
Parsec.parse(["-G"], { sliceIndex: 0 })
  .options(["G", "g", "great"])
  .options(["r", "R"], { default: 8 })
```
```json
{
  "G":true,
  "g":true,
  "great":true,
  "r":8,
  "R":8
}
```

You can negate options using a `--no-` prefix before an option.

```js
Parsec.parse(["--no-woman-no-cry", "--no-wonder=false"],
  { sliceIndex: 0 })
```
```json
{
  "woman-no-cry": false,
  "wonder": true
}
```

## API

### __`Parsec.parse(args, opts)`__

The only one method you will probably deal with.

#### `opts`
  + `operandsKey = "_"`
  + `sliceIndex = 2`
  + `noFlags = true`

### `Parsec.prototype.tuples`

Returns an iterator that yields objects of the form `{ prev, curr, next }` for each item in a list.

### `Parsec.prototype.map`

Maps CLI arguments to custom _Token_ objects.

  + Short Options
  ```js
  { isShort, tokens }
  ```

  + Long Options
  ```js
  { isLong, key, value, token }
  ```

  + Operands
  ```js
  { token, isBare }
  ```

### `Parsec.prototype.shorts`

Token sub-iterator for short options.

### `Parsec.prototype.tokens`

Token iterator for options:
  ```js
  { curr, next, value }
  ```

### `Parsec.prototype.entries`

Iterator for entries:
  ```js
  { key, value }
  ```

### Properties

#### `operandsKey = "_"`

Use a different key name for the operands list.

#### `noFlags = true`

> Set to `false` to opt out.

```js
Parsec.parse(["--no-love=false", "--no-war", "--no-no", "ok"],
  { sliceIndex: 0 })
```
```json
  {
    "love": true,
    "war": false,
    "no-no": "ok"
  }
```

# Example

If your _app_ receives the arguments `./app --foo bar -t now -xzy`:

```js
Parsec.parse(process.argv)
```

will return the following object:

```json
{
  "foo": "bar",
  "t": "now",
  "x": true,
  "z": true,
  "y": true
}
```
This object also has an `options` method that you can use to customize the parsed options:

```js
Parsec.parse(process.argv)
  .options("foo", { default: "hoge" })
  .options("time", { default: 24 })
  .options("xoxo")
  .options("yoyo")
  .options("zorg")
```

returns the following object:

```json
{
   "f": "bar",
   "foo": "bar",
   "t": "now",
   "time": "now",
   "x": true,
   "xoxo": true,
   "z": true,
   "zorg": true,
   "y": true,
   "yoyo": true
 }
 ```

Note the first letter of the option string is used as the short option flag by default. You can opt out of this by specifying an array with the mappings you prefer:

```js
.options(["foo", "F"])
```

# About

Why another options CLI parser?. There are several options out there. There is [`yargs`](https://github.com/bcoe/yargs) and [`commander`](https://github.com/tj/commander.js) (_> 1000 LOC_) which are fun to use, but end up doing too much.

There is also [`minimist`](https://github.com/substack/minimist) and [`nopt`](https://github.com/npm/nopt), which are leaner and offer a more limited feature set, but I wasn't comfortable with their traditional design and large code base. I also found both slightly _too verbose_ for my taste.

I decided to write my own solution (though it [wasn't the first time](https://github.com/bucaran/getopts)) using ES6.

The end result is ~150 LOC and a readable code base.

# Development

```sh

git clone https://github.com/bucaran/parsec
cd parsec
npm install
npm run build
```

# Roadmap ✈

+ Type support.
+ Invalid option checks.

# License

[MIT](http://opensource.org/licenses/MIT) © [Jorge Bucaran][Author]

[Author]: http://about.bucaran.me
[TravisLogo]: http://img.shields.io/travis/bucaran/parsec.svg?style=flat-square
[Travis]: https://travis-ci.org/bucaran/parsec
