# 03 — Developing with Node

Status: started project setup and CommonJS imports and exports. Extend this file as the lessons introduce more topics.

## 1. Project setup: what I am organizing

A project can contain an entry point, modules, exercises, and notes. Separating code into modules helps give each file a clear responsibility.

For the current example:

| File | Responsibility |
| --- | --- |
| `app.js` | Load the template module and print its exported value. |
| `js-foundation/01-template.js` | Define and export an email template. |

`package.json` describes an npm project and can contain metadata, dependencies, and scripts. Record the exact setup commands and scripts from the lesson once practiced.

## 2. CommonJS: exporting a value

```js
// js-foundation/01-template.js
const emailTemplate = `
<div>
  <h1>Hi, {{name}}</h1>
  <p>Thank you for your order.</p>
</div>`;

module.exports = {
  emailTemplate,
};
```

`module.exports` defines what this CommonJS module exposes. Here it exports an object with an `emailTemplate` property.

The shorthand `{ emailTemplate }` is equivalent to `{ emailTemplate: emailTemplate }`.

The backticks create a template literal, which supports multiline strings. `{{name}}` is ordinary text here; Node does not automatically replace it. JavaScript template-literal interpolation uses `${expression}` instead.

## 3. CommonJS: importing with destructuring

```js
// app.js
const { emailTemplate } = require('./js-foundation/01-template');

console.log(emailTemplate);
```

`require` loads the module and returns its exported value. Destructuring extracts the `emailTemplate` property into a local variable.

`./` refers to the importing file's directory for this relative module path. This differs from relative file paths passed to `fs`, which use the process's current working directory.

## 4. Alternative: keep the exported object

```js
const templateExports = require('./js-foundation/01-template');

console.log(templateExports.emailTemplate);
```

Both approaches access the same exported property. Choose one and use the corresponding variable consistently.

### A mistake to remember

```js
const { emailTemplate } = require('./js-foundation/01-template');

// Incorrect: templateExports was never declared in this example.
console.log(templateExports.emailTemplate);

// Correct for the destructured import above:
console.log(emailTemplate);
```

The incorrect statement throws a `ReferenceError` if no `templateExports` variable exists in scope.

## 5. File boundaries matter

The template definition belongs in its module; the importing code belongs in the entry file. Separate lesson examples should not automatically be pasted into one file.

Declaring `const fs` twice in the same scope produces a syntax error. Declaring it once in each of two separate modules is valid.

These examples use CommonJS. Module syntax and file extensions depend on project configuration; revisit ES modules when the course introduces them.

## Practice without the video

1. Create the template module and import it from memory.
2. Add and export a second value, such as `subject`.
3. Import both values using destructuring.
4. Try the object-based import and explain what changed.
5. Rename the imported local variable using destructuring: `const { emailTemplate: template } = require('./js-foundation/01-template');`.
6. Replace `{{name}}` explicitly with a name and print the returned string. Explain whether the original string changed.

## Upcoming topics — add notes after studying

- [ ] Project setup commands and `package.json` scripts.
- [ ] Nodemon and third-party packages.
- [ ] Environment variables.
- [ ] Debugging with breakpoints.
- [ ] Callbacks and arrow functions.
- [ ] Factory functions and adapters.
- [ ] Promises and promise chaining.
- [ ] Async/await and HTTP requests.

## Review checklist

- [ ] Explain what the template module exports.
- [ ] Import an exported property using both approaches.
- [ ] Explain the difference between a module path and an `fs` file path.
- [ ] Diagnose the undefined `templateExports` variable.
- [ ] Explain why `{{name}}` is printed literally.
- [ ] Recreate the two-file example without copying it.

## References

- [Node.js CommonJS modules](https://nodejs.org/api/modules.html)
- [Course syllabus](https://cursos.devtalles.com/courses/nodejs-de-cero-a-experto)
