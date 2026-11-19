# 03 — Developing with Node.js

> **Progress:** Project setup, CommonJS modules, Nodemon, environment variables, debugging, callbacks, and arrow functions.

## 1. Project Setup

A Node.js project can contain an entry point, modules, configuration files, dependencies, and documentation.

Separating code into modules allows each file to have a clear responsibility, making the project easier to understand, maintain, and scale.

### Project structure

```text
03-developing-with-node/
├── js-foundation/
│   └── 01-template.js
├── app.js
├── package.json
├── package-lock.json
├── .gitignore
└── node_modules/
```

| File | Responsibility |
|---|---|
| `app.js` | Main entry point of the application. |
| `js-foundation/01-template.js` | Defines and exports an email template. |
| `package.json` | Defines project metadata, scripts, and dependencies. |
| `package-lock.json` | Records the resolved dependency tree for reproducible installations. |
| `.gitignore` | Specifies files and directories Git should ignore. |
| `node_modules/` | Contains installed npm packages. |

### Basic npm commands

```bash
npm init -y
npm install
npm install <package-name>
npm install -D <package-name>
npm run dev
```

- `npm init -y` — Initializes a project with default settings.
- `npm install` — Installs the project's declared dependencies.
- `npm install <package-name>` — Installs a production dependency.
- `npm install -D <package-name>` — Installs a development dependency.
- `npm run dev` — Executes the `dev` script defined in `package.json`, if present.

**Key concept:** npm manages dependencies and scripts, while Node.js executes JavaScript.

---

## 2. CommonJS: Exporting Values

**CommonJS** is a module system supported by Node.js. It allows code to be divided into separate files that share functionality using `module.exports` and `require()`.

### Example: Exporting an email template

```js
// js-foundation/01-template.js

const emailTemplate = `
<div>
  <h1>Hi, {{name}}</h1>
  <p>Thank you for your order.</p>
</div>
`;

module.exports = {
  emailTemplate,
};
```

### Explanation

- `const emailTemplate` — Declares a variable containing a string.
- Backticks (`` ` ``) — Create a template literal, allowing multiline strings and JavaScript interpolation.
- `module.exports` — Defines the value exposed by the module.
- `{ emailTemplate }` — Creates an object containing a property named `emailTemplate`.

The following expressions are equivalent:

```js
module.exports = { emailTemplate };

// Equivalent to:

module.exports = {
  emailTemplate: emailTemplate,
};
```

**Important:** `{{name}}` is literal text in this example. JavaScript does not automatically replace it.

JavaScript template-literal interpolation uses `${expression}`:

```js
const name = 'John';
const greeting = `Hello, ${name}`;

console.log(greeting); // Hello, John
```

---

## 3. CommonJS: Importing with Destructuring

The `require()` function loads a CommonJS module and returns its exported value.

### Example

```js
// app.js

const { emailTemplate } = require('./js-foundation/01-template');

console.log(emailTemplate);
```

### How it works

1. `require()` loads the specified module.
2. Node.js evaluates the module if it has not already been loaded into the CommonJS module cache.
3. The module returns its exported value.
4. Destructuring extracts the `emailTemplate` property into a local variable.
5. `console.log()` prints the template.

**Important concepts:**

- `require()` imports CommonJS modules.
- `{ emailTemplate }` uses object destructuring.
- `./` indicates a relative module path, resolved from the importing module's directory.
- The `.js` extension can be omitted for this local CommonJS example.

### Module paths vs. filesystem paths

Relative paths passed to `require()` are resolved relative to the importing module.

By contrast, relative paths used with many `fs` operations are resolved relative to the process's current working directory (`process.cwd()`).

This distinction matters when working with files and directories.

---

## 4. Alternative: Importing the Entire Object

Instead of extracting a property using destructuring, we can store the entire exported object.

```js
const templateExports = require('./js-foundation/01-template');

console.log(templateExports.emailTemplate);
```

### Comparison

**With destructuring:**

```js
const { emailTemplate } = require('./js-foundation/01-template');

console.log(emailTemplate);
```

**Without destructuring:**

```js
const templateExports = require('./js-foundation/01-template');

console.log(templateExports.emailTemplate);
```

Both approaches access the same exported property.

### Common mistake: Undefined variable

```js
const { emailTemplate } = require('./js-foundation/01-template');

// Incorrect:
console.log(templateExports.emailTemplate);

// Correct:
console.log(emailTemplate);
```

The incorrect statement produces a `ReferenceError` if `templateExports` has not been declared.

**Key takeaway:** Destructuring creates local variables for selected properties. Importing the whole object requires accessing those properties through the object.

---

## 5. Module Boundaries and Scope

Each module should have a clearly defined responsibility.

For example:

- `01-template.js` defines and exports the email template.
- `app.js` imports and uses the email template.

Module boundaries also prevent top-level declarations in separate CommonJS files from conflicting with each other.

### Example

```js
// file-a.js
const fs = require('node:fs');
```

```js
// file-b.js
const fs = require('node:fs');
```

Both declarations are valid because they belong to separate modules.

However, declaring the same `const` twice within one scope produces a `SyntaxError`.

**Note:** These examples use CommonJS. ES Modules use `import` and `export`, which will be studied separately.

---

## 6. Nodemon

**Nodemon** is a development utility that monitors files and automatically restarts a Node.js application when relevant changes are detected.

### Why use Nodemon?

Normally, after modifying application code, we must stop and restart the Node.js process manually.

Nodemon automates this process and makes development more convenient.

### Installation

```bash
npm install -D nodemon
```

The `-D` flag installs Nodemon as a development dependency.

### Configuring npm scripts

Inside `package.json`:

```json
{
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
  }
}
```

Run the application using:

```bash
npm run dev
```

Now, Nodemon monitors relevant files and restarts the application when changes are detected.

### Alternative: Native Node.js Watch Mode

Modern Node.js versions support a built-in watch mode:

```bash
node --watch app.js
```

This can automatically restart the application without installing Nodemon.

**Key takeaway:** Nodemon and `node --watch` are development tools. They restart processes when files change; neither provides hot module replacement automatically.

---

## 7. Environment Variables

**Environment variables** are configuration values supplied to an application by the environment in which it runs.

They help separate configuration from application logic.

### Why use environment variables?

Applications often require different configurations for development, testing, and production.

Common examples include:

- Server ports.
- Database connection strings.
- API configuration.
- Credentials and secret keys.
- Application environment settings.

### Example: `.env`

```dotenv
PORT=3000
APP_NAME=NodeLearning
NODE_ENV=development
```

### Accessing environment variables

```js
const port = Number(process.env.PORT ?? 3000);
const appName = process.env.APP_NAME ?? 'MyApp';

console.log(port);
console.log(appName);
```

### Explanation

- `process.env` — Provides access to environment variables.
- `process.env.PORT` — Reads the value of `PORT`.
- `Number()` — Converts the string into a number.
- `??` — Uses the fallback value when the original value is `null` or `undefined`.

Environment variables are exposed as strings, so conversion and validation may be necessary.

### Loading a `.env` file

Node.js supports loading environment files through its native CLI option:

```bash
node --env-file=.env app.js
```

The file is not automatically loaded just because it exists in the project.

### Security considerations

Never commit real passwords, API keys, or other secrets to Git.

Example `.gitignore`:

```gitignore
node_modules/
.env
```

A `.env.example` file can document the required environment variables without exposing their real values.

**Key takeaway:** Environment variables make applications easier to configure across different environments, but they must be validated and handled securely.

---

## 8. Debugging with Breakpoints

**Debugging** is the process of examining application execution to identify errors and understand how code behaves.

A breakpoint temporarily pauses the program at a selected line.

### Example

```js
function calculateTotal(price, quantity) {
  const subtotal = price * quantity;
  const tax = subtotal * 0.16;

  return subtotal + tax;
}

const total = calculateTotal(100, 2);

console.log(total);
```

Set a breakpoint at:

```js
const tax = subtotal * 0.16;
```

When execution pauses, inspect the current values of `price`, `quantity`, and `subtotal`.

### Debugger operations

| Operation | Purpose |
|---|---|
| Breakpoint | Pause execution at a particular line. |
| Continue | Resume execution until the next breakpoint or program completion. |
| Step Over | Execute a line without entering its function calls. |
| Step Into | Enter a function to inspect its execution. |
| Step Out | Complete the current function and return to its caller. |
| Variables | Inspect the values of variables in scope. |
| Call Stack | Inspect the sequence of active function calls. |

### Why is debugging useful?

Using `console.log()` can help inspect values, but a debugger provides greater control over execution.

It allows you to understand how variables change, how functions interact, and where unexpected behavior begins.

**Key takeaway:** Debugging is not only for fixing errors. It is also an effective way to understand unfamiliar code.

---

## 9. Callbacks and Arrow Functions

### 9.1 Arrow Functions

Arrow functions provide an alternative, usually more concise syntax for defining functions.

**Traditional function:**

```js
function multiply(a, b) {
  return a * b;
}
```

**Arrow function:**

```js
const multiply = (a, b) => a * b;
```

Both return the multiplication result.

### Implicit and explicit returns

An arrow function can return an expression implicitly:

```js
const sum = (a, b) => a + b;
```

When curly braces define the function body, an explicit `return` is required to return a value:

```js
const sum = (a, b) => {
  return a + b;
};
```

**Important:** Arrow functions are not identical to traditional functions. In particular, they do not have their own `this` binding.

### 9.2 Callbacks

A **callback** is a function passed as an argument to another function, allowing the receiving function to invoke it.

### Example

```js
function processOrder(orderId, callback) {
  console.log(`Processing order ${orderId}`);

  callback();
}

processOrder(123, () => {
  console.log('Order processed!');
});
```

### Execution flow

1. `processOrder()` is called with an order ID and a function.
2. The order-processing message is printed.
3. `callback()` executes the function received as an argument.
4. The completion message is printed.

Expected output:

```text
Processing order 123
Order processed!
```

**Important:** A callback is not necessarily asynchronous. This example executes synchronously.

### 9.3 Asynchronous Callbacks

Asynchronous operations allow certain tasks to be scheduled or performed without blocking the execution of subsequent synchronous code.

### Example

```js
console.log('First');

setTimeout(() => {
  console.log('Second');
}, 1000);

console.log('Third');
```

Expected output:

```text
First
Third
Second
```

### Why does this happen?

1. `First` is printed.
2. `setTimeout()` schedules the callback to run after at least approximately one second.
3. JavaScript continues executing the synchronous code.
4. `Third` is printed.
5. Once the timer is ready and the event loop can execute its callback, `Second` is printed.

**Key takeaway:** Passing a callback does not automatically make an operation asynchronous. Its behavior depends on how and when the receiving function executes it.

### 9.4 Error-First Callbacks

Many traditional Node.js APIs follow the **error-first callback** convention.

The first callback parameter represents an error, if one occurred. Subsequent parameters contain results.

### Example

```js
const fs = require('node:fs');

fs.readFile('./notes.txt', 'utf8', (err, data) => {
  if (err) {
    console.error(err);
    return;
  }

  console.log(data);
});
```

### Explanation

- `fs.readFile()` — Reads a file asynchronously.
- `err` — Contains an error if the operation fails.
- `data` — Contains the file content when the operation succeeds.
- `return` — Exits the callback after handling the error.

**Key takeaway:** Understanding callbacks is essential before studying Promises, async/await, and asynchronous operations in Node.js.

---

## Next Topics

Add each topic after completing its lesson, following the same structure: **Definition → Why it matters → Example → Explanation → Common mistakes.**

- [ ] Factory functions and adapters
- [ ] Promises and promise chaining
- [ ] Async/await
- [ ] HTTP requests

