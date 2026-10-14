# 02 — Node Fundamentals: First Steps

## 1. Running JavaScript with Node

Node.js is a JavaScript runtime that can execute JavaScript outside a browser. It provides APIs for tasks such as accessing files.

```js
const message = 'Hello world';
console.log(message);
```

Run a file from the terminal:

```bash
node app.js
```

`console.log` prints a value. Top-level synchronous statements execute in order, unless control flow or an error changes execution.

## 2. Reading and writing text files

```js
const fs = require('node:fs');

const data = fs.readFileSync('README.md', 'utf-8');
const newData = data.replace(/React/gi, 'Angular');

fs.writeFileSync('README-Angular.md', newData);
```

- `fs` is built into Node; it does not require an npm installation.
- Both `require('fs')` and `require('node:fs')` work for this built-in module.
- Specifying UTF-8 makes the read return text. Without an encoding, it returns a `Buffer`.
- These synchronous operations block JavaScript execution on the calling thread until they finish.
- Relative file paths are resolved from the process's current working directory, not automatically from the script's directory.
- Reading a missing file throws an error.
- Writing creates a file or replaces the contents of an existing file by default. It does not automatically create missing parent directories.
- `replace` returns a new string; `data` stays unchanged.

### Regular expression flags

In `/React/gi`, `g` means global matching and `i` means case-insensitive matching. This replaces matching substrings, including matches inside larger words.

### Check my understanding

- What changes if I remove `g`? What changes if I remove `i`?
- What happens if I run the script from a different directory?
- Can I explain why this script is useful for a small local task, but blocking operations deserve care in a server?

## 3. Counting words and matches

```js
const fs = require('node:fs');
const content = fs.readFileSync('README.md', 'utf-8');

const words = content.match(/\S+/g) ?? [];
const reactMatches = content.match(/react/gi) ?? [];

console.log('Words:', words.length);
console.log('React matches:', reactMatches.length);
```

Here, a “word” means a non-whitespace sequence. This is a simple exercise definition, not full natural-language word detection.

`split(' ')` splits only on literal spaces. Repeated spaces can create empty entries, and line breaks or tabs are not treated as separators. `/\S+/g` finds groups of non-whitespace characters and handles empty input with the fallback.

With a global regex, `match` returns an array of matching strings, or `null` if there are no matches. `??` uses the value on its right when the value on its left is `null` or `undefined`.

### A mistake to remember

```js
// Incorrect: the fallback is applied to the regex, not the match result.
content.match(/react/gi ?? []).length;

// Correct: handle a null match result before reading its length.
const reactWordCount = (content.match(/react/gi) ?? []).length;
```

`/react/gi` matches substrings: `React`, `REACT`, and the beginning of `reactive` all count.

For this English-word exercise, word boundaries give a more specific count:

```js
const reactWordCount = (content.match(/\breact\b/gi) ?? []).length;
```

Regex word boundaries are not a universal definition of words for every language or punctuation convention.

### Check my understanding

Predict and then run both regexes against:

```js
const content = 'React REACT reactive\nreact';
```

Expected: four substring matches, three word-boundary matches, and four whitespace-separated words. Also try empty input and input with no React matches.

## 4. Execution order and timers

```js
console.log('Program starts');

setTimeout(() => {
  console.log('First timeout');
}, 3000);

setTimeout(() => {
  console.log('Second timeout');
}, 0);

setTimeout(() => {
  console.log('Third timeout');
}, 0);

console.log('Program ends');
```

Expected output for this example:

```text
Program starts
Program ends
Second timeout
Third timeout
First timeout
```

- Calling `setTimeout` registers a callback and returns; it does not pause the following statements.
- A delay of zero does not mean the callback runs immediately.
- A timer delay is a scheduling threshold, not a promise of an exact execution time.
- Once the current JavaScript work finishes, Node can run eligible timer callbacks through its event loop.
- Long-running synchronous JavaScript can delay callbacks whose timers have already elapsed.
- This example is not a universal ordering rule for all asynchronous APIs. Node does not guarantee exact timer timing or ordering in general.

### Check my understanding

- Why does `Program ends` appear before the zero-delay callbacks?
- What happens if I add another synchronous `console.log` at the bottom?
- Could a three-second timer execute later than three seconds? Why?

## Review checklist

- [ ] Run a script from the terminal.
- [ ] Read text, transform it, and write a different file.
- [ ] Explain UTF-8, relative paths, and synchronous file operations.
- [ ] Handle a regex with no matches.
- [ ] Distinguish substring matching from word-boundary matching.
- [ ] Explain the timer example without running it first.

## References

- [Node.js file system](https://nodejs.org/api/fs.html)
- [Node.js timers](https://nodejs.org/api/timers.html)
