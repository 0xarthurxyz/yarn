# Yarn (Cheat Sheet)

## `yarn init`

Use `yarn init` to initialize a Node.js project and create a `package.json` file.

## `yarn add --dev` and `yarn remove`

Use `yarn add --dev` to add a package to the `devDependencies` section of `package.json`.
Or use `yarn add -D` as a shortcut.

## `yarn link` example

How to use a local branch of an NPM project for testing purposes, e.g. a local branch of `viem`.

1.  Checkout the local branch of `viem` that you want to test (`git checkout <branch>`)

1.  Build the local branch of `viem` (using `bun install` and `bun run build` in this case)

1.  Navigate to the output directory in `viem` (in this case `src`)

1.  Run [`yarn link`](https://yarnpkg.com/cli/link) to create a symlink to the local branch 
    of `viem`. 
    
    This will create a symlink in the global `node_modules` directory, which 
    can be used by other projects.
1.  Navigate to the project that will use the local branch of `viem` (in this case 
    [`celo-org/txtypes`](https://github.com/celo-org/txtypes))

1.  Run [`yarn link viem`](https://yarnpkg.com/cli/link) to create a symlink to the local 
    branch of `viem`. 
    
    This will create a symlink in the local `node_modules` directory, 
    which will be used by the project. If you run `yarn install` in the project, it will
    use the local branch of `viem` instead of the version from npm. You can see this in the 
    `package.json` file of the project, which will have a line like this:
    
    ```json
    "viem": "link:../viem/src"
    ```

1.  Work and test with the local branch of `viem` in the project as though it was a published
    version of `viem` (`celo-org/txtypes` in this case)

1.  When you are done, run [`yarn unlink viem`](https://yarnpkg.com/cli/unlink) in the project
    (`celo-org/txtypes` in this case) to remove the symlink to the local branch of `viem`. 
    
    This will remove the line from the `package.json` file of the project, and 
    will remove the symlink from the local `node_modules` directory. If you run `yarn install` 
    in the project, it will use the version of `viem` from npm again.

1.  Run [`yarn unlink`](https://yarnpkg.com/cli/unlink) in the output directory of `viem` 
    (`src` in this case) to remove the symlink to the local branch of `viem`. 
    
    This will remove the symlink from the global `node_modules` directory.
