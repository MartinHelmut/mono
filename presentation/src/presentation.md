Theme: Scherzkeks,0
slidenumbers: true
footer: **Speaking about mono repos ...**, Martin Fieber (@MartinFieber) // SinnerSchrader Agency Day 2018.12

# [fit] Speaking about **mono repos** ...

---

### (or lets call it "multi project management")

---

## [fit] ... there is more than one option,
## and here they are.

---

[.autoscale: true]
[.build-lists: true]

# Contents

* Who am I?
* What do I mean with options?
* Some clarification
* Number 1, 2, 3 and 4

---

# Who am I?

---

![original](images/martin_fieber.png)

**Senior Product Engineer @ S2**
**_@MartinHelmut_**

---

# What do I mean with **options**?

---

# [fit] **Different tools**, different needs

^ Depending on what you want to achieve, different ways to get there
From full blown tools managing you projects to small install helper

---

# Some clarification

^ Therefore, some clarification are needed

---

## [fit] No **A** versus **B**

^ This is not a comparison
Depending on what you need, you should choose the tool for your need
I just want to show 4 options available
This also means that this list will not be complete, there are more options still out there

---

# [fit] Let's start

---

# Number **1**, **2**, **3** and **4**

^ Let's give this options a name ... (next slide)

---

# Options

[.autoscale: true]
[.build-lists: true]

* TypeScript "--build"
* Yarn workspaces
* Lerna
* Rush.js

---

# [fit] Setup

^ First things first, I use the same projects for every example
And this is how it looks like ... (next slide)

---

```
app/
    src/
        index.ts
utils/
    src/
        index.ts
```

^ Simple application that depends on utils
Here is utils ... (next slide)

---

```typescript
// utils/src/index.ts
export function getHello(name: string): string {
    return `Hello, ${name}`;
}
```

^ And here is the app ... (next slide)

---

```typescript
// app/src/index.ts
import { getHello } from '@scope/utils';

console.log(getHello('Everyone'));
```

^ This is the basic setup that will be used for every example

---

# TypeScript "--build"

^ Let's se how we can achieve this with the TypeScript compiler

---

`npm i typescript`

^ Overall can be installed with

---

```
root/
    src/
        app/
            index.ts
            tsconfig.json
        utils/
            index.ts
            tsconfig.json
        tsconfig.json
    package.json
    tsconfig.json
```

^ Here is the folder structure and you already see that the `tsconfig.json` defines projects
The root `tsconfig.json` defines the basic compiler options ... (next slide)

---

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "target": "es5",
        "strict": true,
        "declaration": true,
        "baseUrl": "src",
        "composite": true,
        "outDir": "./dist"
    }
}
```

^ // tsconfig.json
The tsconfig in my `src` folder defines my project references ... (next slide)

---

```json
{
    "extends": "../tsconfig.json",
    "references": [
        { "path": "./app" },
        { "path": "./utils }
    ]
}
```

^ // src/tsconfig.json
The source config extends the compiler options and defines the project references
And the project configurations add custom settings needed as well as the `composite` property to make it happen

---

```json
{
    "extends": "../tsconfig.json",
    "compilerOptions": {
        "baseUrl": "./",
        "declarationMap": true,
        "composite": true
    },
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules"
    ]
}
```

^ // src/utils/tsconfig.json
This extends the source config, defines some compiler options like the needed `composite` and includes necessary files

---

```typescript
import { getHello } from '../utils';
```

^ Imports from app to utils would look like this (relative imports)
The `package.json` looks like this: (next slide)

---

```json
{
    "name": "root",
    "private": true,
    "main": "dist/src/app/index.js",
    "scripts": {
        "build": "tsc --build",
        "start": "node dist/src/app/index.js"
    },
    "devDependencies": {
        "typescript": "^3.2.1"
    }
}
```

^ The build command is `tsc --build`.

---

# [fit] **Why?**

---

"By doing this, you can greatly improve build times, enforce logical separation between components, and organize your code in new and better ways."

^ This is taken directly from TypeScrips documentation about this feature
And more or less gives you the benefit, faster builds with TypeScript only projects
But it would be more effort to get separate deployable packages out of this
Also there is out-of-the-box no way to manage separated dependencies

---

_typescriptlang.org/docs/handbook/project-references.html_

^ And here you can find the documentation

---

# Yarn workspaces

^ Let's se how we can achieve multi project management with Yarn workspaces
How to install Yarn you can find here: (next slide)

---

_yarnpkg.com/docs/install_

---

```
root/
    @scope/
        app/
            src/
                index.ts
            package.json
            tsconfig.json
        utils/
            src/
                index.ts
            package.json
            tsconfig.json
    package.json
```

^ Here is the folder structure
Here you see that this more resembles separated packages
The folder `@scope` is intended to get imports like: (next slide)

---

```typescript
import { getHello } from '@scope/utils';
```

^ The workspace setting is included inside the package.json (next slide)

---

```json
{
    "name": "root",
    "private": true,
    "workspaces": [
        "@scope/*"
    ],
    "devDependencies": {
        "typescript": "^3.2.1"
    }
}
```

^ This defines that everything in `@scope` is a workspace
This will hoist the dependencies to the root
Therefore if I execute yarn ... (next slide)

---

```shell
$ yarn
```

^ It will install all the dependencies on root level.
And connects the inner dependencies, e.g.: (next slide)

---

```json
{
    "dependencies": {
        "@scope/utils": "1.0.0"
    }
}
```

^ But Yarn will not help redirecting commands to every sub-project
So I need to build all by packages
Or I combine this with TypeScript --build if I use TypeScript projects

---

# [fit] This is up to you 🤓

---

# [fit] **Why?**

^ Where TypeScript --build gave you a central way to build projects,
Yarn gives a central way to install dependencies for your project
Think about combining this and you get almost a fancy MonoRepo solution

---

_yarnpkg.com/docs/workspaces_

^ Documentation

---

# Lerna

^ Let's get to a more comprehensive way to manage multiple projects: Lerna

---

"Lerna is a tool that optimizes the workflow around managing multi-package repositories with git and npm."

^ This means it gives you access to ... (next slide)

---

[.autoscale: true]
[.build-lists: true]

* Dependency management
* Command execution
* Version management
* Publishing

^ Projects using Lerna: Babel, React, Angular and Jest

---

```
root/
    packages/
        app/
            src/
                index.ts
            package.json
            tsconfig.json
        utils/
            src/
                index.ts
            package.json
            tsconfig.json
    lerna.json
    package.json
```

^ This is how it could look like, with every projects by default inside `packages`
The `lerna.json` file is the basic configuration (next slide)

---

```json
{
    "packages": [
        "packages/*"
    ],
    "version": "independent"
}
```

^ When using a command like `lerna init` this will be the created default
Package versions are independent here (this was done by a flag)
To generate this and get help creating a mono repo you can use: (next slide)

---

`npx lerna init --independent`

^ Hint: If creating a repo with Lerna you could combine this with Yarn workspaces as well

---

## [fit] Run and execute

^ Lerna now can execute or pass commands for you
In your root `package.json` you can define this in two different ways (next slide)

---

```json
{
    "scripts": {
        "test": "lerna run --parallel test",
        "clean": "lerna exec -- rm -fr dist"
    }
}
```

^ First version executes the npm-script `test` in all your packages
The second version executes the command `rm` directly in every package
Lerna can also handle versioning of your packages ... (next slide)

---

`npx lerna version`

---

[.autoscale: true]
[.build-lists: true]

* Identifies packages that have been updated since the previous tagged release.
* Prompts for a new version.
* Modifies package metadata to reflect new release.
* Commits those changes and tags the commit.
* Pushes to the git remote.

^ This also gives you help for publishing ... (next slide)

---

`npx lerna publish`

^ Executes `lerna version` and publish packages with new versions to your defined registry
But Lerna can also ... (next slide)

---

[.autoscale: true]
[.build-lists: true]

* Generate Changelogs
* Conventional commits support
* Hoist dependencies without Yarn

^ For more features and details you can view the documentation here ... (next slide)

---

_lernajs.io_

^ And if you want to start with Lerna there is a helpful tool (next slide)

---

_github.com/szarouski/lerna-wizard_

^ This gives you guidance for working with a Lerna MonoRepo
And there is still option 4 ... (next slide)

---

# Rush.js

^ Developed by Microsoft

---

"... JavaScript developers who build and publish many packages from a common Git repo."

^ Taken from the Documentation
Similar to Lerna, but with some differences (next slide)

---

## [fit] Configuration 🔧

^ One of this is you have to configure more
But this also gives you more options for the "How"
For this you need to install rush ... (next slide)

---

`npm i -g @microsoft/rush`

^ Lets view the Rush.js repo setup: (next slide)

---

```
root/
    apps/
        app/
            src/
                index.ts
            package.json
            tsconfig.json
    libraries/
        utils/
            src/
                index.ts
            package.json
            tsconfig.json
    common/config/rush
        // ...
    rush.json
```

^ There are category folder `apps` and `libraries` as well as a `common` that can contain configurations
Also there is no root `package.json` but a `rush.json` for the basic configuration (next slide)

---

```json
{
    "$schema": "https://developer.microsoft.com/json-schemas/rush/v5/rush.schema.json",
    "rushVersion": "5.5.2",
    "pnpmVersion": "2.15.1",
    "nodeSupportedVersionRange": ">=8.9.4 <11.0.0",
    "projectFolderMinDepth": 2,
    "projectFolderMaxDepth": 2,
    "projects": [
        {
            "packageName": "@scope/app",
            "projectFolder": "apps/app"
        },
        {
            "packageName": "@scope/utils",
            "projectFolder": "libraries/utils"
        }
    ]
}
```

^ This is how the `rush.json` looks like, but let's get through it step by step (next slide)

---

```json
{
    "$schema": ".../json-schemas/rush/v5/rush.schema.json",
    "rushVersion": "5.5.2",
    "pnpmVersion": "2.15.1",
    "nodeSupportedVersionRange": ">=8.9.4 <11.0.0",
}
```

^ First line url to schema, second rush, third package manager, and node range
Next two options ... (next slide)

---

```json
{
    "projectFolderMinDepth": 2,
    "projectFolderMaxDepth": 2,
}
```

^ ... enforce that category folder will be used for all projects

---

```json
{
    "projects": [
        {
            "packageName": "@scope/app",
            "projectFolder": "apps/app"
        },
        {
            "packageName": "@scope/utils",
            "projectFolder": "libraries/utils"
        }
    ]
}
```

^ And you have to specify all projects you use with package name and folder
But there are more options
This can be default generated with ... (next slide)

---

`rush init`

^ This will give you good basic setup and create a `rush.json` with a lot of documentation for every option
Some of the options are ... (next slide)

---

## Event Hooks

```json
{
    "eventHooks": {
        "preRushBuild": [
            "path to a script"
        ]
    }
}
```

---

## Git policies

```json
{
    "gitPolicy": {
        "allowedEmailRegExps": []
    }
}
```

^ Restrict allowed emails
And define commands ... (next slide)

---

## [fit] Commands

---

`common/config/rush/command-line.json`

^ Located in its own configuration file, and could contain: (next slide)

---

```json
{
    "commands": [
        {
          "commandKind": "bulk",
          "name": "test",
          "summary": "Test each project sources",
          "description": "Here is a long description ..."
        }
    ]
}
```

^ Bulk command that executes the npm-script `test` in every project
But it could also be a global command:

---

```json
{
    "commands": [
        {
            "commandKind": "global",
            "name": "deploy",
            "summary": "Deploy applications to ...",
            "description": "...",
            "shellCommand": "path/to/deploy.script"
        }
    ]
}
```

---

[.autoscale: true]
[.build-lists: true]

## Other features worth to mention?

* Version policies
* Publish packages
* Changelog generation
* Scan and integrity checks

---

_rushjs.io_

^ Lots more here

---

# To much 😨

---

_github.com/MartinHelmut/mono_

^ Here you can find not only the presentation
But working examples for all these techniques

---

# [fit] Thanks
