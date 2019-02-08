# types-starter

Starter repo for internally-hosted DefinitelyTyped.

## Motivation

DefinitelyTyped is great for hosting type declarations for public JS npm packages. But, companies often publish their internal npm packages to a private npm registry, hosted internally. How can we add types for those internal JS packages?

We can mirror the DefinitelyTyped solution for this by creating an internal repo that contains the type declaration files for internal packages and publish them to their own npm scope. Suppose we are working at a company called "acme". Similar to DefinitelyTyped `@types` packages, we can publish to `@acme-types` instead. This repo offers a setup modeled after DefinitelyTyped, where we can add, test, and publish our "acme" types.

## How to add a types package

Create a new folder in the types folder.

For example, if we want to add types for "acme-package", we'd add a `types/acme-package` folder.

### tsconfig.json

Create a tsconfig.json file with the following default configuration:

```json5
{
  "compilerOptions": {
    "module": "commonjs",
    "lib": ["es6", "dom"],
    "noImplicitAny": true,
    "noImplicitThis": false,
    "strictNullChecks": true,
    "strictFunctionTypes": false,
    "baseUrl": "../",
    "typeRoots": ["../"],
    "types": [],
    "noEmit": true,
    "forceConsistentCasingInFileNames": true,
    "jsx": "preserve"
  },
  "files": ["index.d.ts"]
}
```

### tslint.json

Create a tslint.json file with the following default configuration:

```json5
{
  "extends": "dtslint/dtslint.json",
  "rules": {
    "no-single-declare-module": false
  }
}
```

### package.json

Create a package.json file with the following contents,
where we use `@acme-types/acme-package` as the name of our new package.

For `version`, match the major and minor versions to the version of the
JS package we're annotating.

And, fill in the `description`.

```json5
{
  "name": "@acme-types/acme-package",
  "version": "1.0.0",
  "description": "TypeScript definitions for Acme Package",
  "license": "MIT",
  "main": "",
  "types": "index",
  "files": ["index.d.ts"],
  "scripts": {
    "test": "dtslint ."
  }
}
```

### index.d.ts

Add an index.d.ts file, where we can add our types.

```typescript
// Acme type definitions for @acme-types/acme-package 1.0.0
// Project: https://github.acme.com/acme-package
// Definitions by: Your User <https://github.acme.com/username>
// Definitions: https://github.acme.com/types
// TypeScript Version: 2.9

declare module "acme-package" {
  function greeter(name: string): string;
}
```

### Testing types

A test file should be a piece of sample code that tests using the library. Tests are type-checked, but not run. To assert that an expression is of a given type, use `$ExpectType`.
To assert that an expression causes a compile error, use `$ExpectError`. (Assertions will be checked by the expect lint rule.)

```
import { f } from "my-lib"; // f is(n: number) => void

// $ExpectType void
f(1);

// Can also write the assertion on the same line.
f(2); // $ExpectType void

// $ExpectError
f("one");
```

## How to publish a types package

Suppose we wanted to publish version 1.0.1 of our types for acme-package.

```
git checkout master
git pull origin master
# make sure we have a clean git state
cd types/acme-package
vim package.json
# update the version
npm publish
git commit -am "acme-package@1.0.1"
git push
```

TODO(brieb): make a command for this

## How to consume a types package

Suppose we have a TS project, where we would like to consume these types.

Install the desired package, along with the types package.

```sh
npm install --save acme-package
npm install --save-dev @acme-types/acme-package
```

Add `@acme-types` to the project's `typeRoots`

```json5
{
  "compilerOptions": {
    // ...
    "typeRoots": [
      "node_modules/@acme-types",
      "node_modules/@types"
    ]
    // ...
  },
  // ...
}
```
