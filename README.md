# Yarn (Cheat Sheet)

## `yarn init`

Use `yarn init` to initialize a Node.js project and create a `package.json` file.

## `yarn add --dev` and `yarn remove`

Use `yarn add --dev` to add a package to the `devDependencies` section of `package.json`. Or use
`yarn add -D` as a shortcut.

## `yarn link` example

How to use a local branch of an NPM project for testing purposes, e.g. a local branch of `viem`.

1.  Checkout the local branch of `viem` that you want to test (`git checkout <branch>`)

1.  Build the local branch of `viem` (using `bun install` and `bun run build` in this case)

1.  Navigate to the output directory in `viem` (in this case `src`)

1.  Run [`yarn link`](https://yarnpkg.com/cli/link) to create a symlink to the local branch of
    `viem`.

    This will create a symlink in the global `node_modules` directory, which can be used by other
    projects.

1.  Navigate to the project that will use the local branch of `viem` (in this case
    [`celo-org/txtypes`](https://github.com/celo-org/txtypes))

1.  Run [`yarn link viem`](https://yarnpkg.com/cli/link) to create a symlink to the local branch of
    `viem`.

    This will create a symlink in the local `node_modules` directory, which will be used by the
    project. If you run `yarn install` in the project, it will use the local branch of `viem`
    instead of the version from npm. You can see this in the `package.json` file of the project,
    which will have a line like this:

    ```json
    "viem": "link:../viem/src"
    ```

1.  Work and test with the local branch of `viem` in the project as though it was a published
    version of `viem` (`celo-org/txtypes` in this case)

1.  When you are done, run [`yarn unlink viem`](https://yarnpkg.com/cli/unlink) in the project
    (`celo-org/txtypes` in this case) to remove the symlink to the local branch of `viem`.

    This will remove the line from the `package.json` file of the project, and will remove the
    symlink from the local `node_modules` directory. If you run `yarn install` in the project, it
    will use the version of `viem` from npm again.

1.  Run [`yarn unlink`](https://yarnpkg.com/cli/unlink) in the output directory of `viem` (`src` in
    this case) to remove the symlink to the local branch of `viem`.

    This will remove the symlink from the global `node_modules` directory.

## Global packages

### Upgrade global package (example `@celo/celocli`)

Source: chatGPT

Upgrade `@celo/celocli` globally with `yarn global add @celo/celocli@latest`.

Check the version afterward with `yarn global list @celo/celocli` (or `celocli --version`).

### Find path to global packages

Source: chatGPT

With `nvm`, each Node.js version has its own separate set of globally installed packages, so if you
switch Node versions with `nvm`, the path to globally installed packages like `celocli` may change.

To find the installation paths of global packages for the current Node version, use:

```sh
$ npm root -g
/Users/arthur/.nvm/versions/node/v18.16.1/lib/node_modules
```

-   This command shows the directory where global **libraries** are installed by npm.
-   The output is the path to the `node_modules` directory where npm stores the globally installed
    packages.
-   For example, if `npm root -g` returns `/usr/local/lib/node_modules`, your global npm packages
    are located in this directory.

```sh
$ yarn global bin
/opt/homebrew/bin
```

-   This command shows the directory where global **executable binaries** installed by Yarn are
    located.
-   This is the directory you need to include in your PATH environment variable to be able to run
    globally installed Yarn packages from the command line.
-   The path provided by `yarn global bin` is not where Yarn's global packages are stored in their
    entirety (like libraries and dependencies), but specifically where the executables (the
    command-line tools) are placed.

Key differences between `npm root -g` and `yarn global bin`. They are similar in that they both
relate to the installation paths of global packages, but they serve slightly different purposes and
can point to different locations.

-   **Location of Installations**: While npm installs both the libraries and their executables in
    the same `node_modules` directory, Yarn typically separates them. Yarn installs the actual
    package contents in a global location which is not directly exposed by `yarn global bin` and
    places the executables in the directory reported by `yarn global bin`.
-   **Integration with the Shell**: For the globally installed packages' binaries to be accessible
    from the command line, the path provided by `yarn global bin` must be added to the PATH
    environment variable. npm, on the other hand, usually handles this automatically during Node.js
    installation, so global npm binaries are typically available in the command line without extra
    configuration.

-   **Differences in Environment Configuration**: Because of these differences, managing global
    packages with npm and Yarn might require different configurations in your shell environment.
    This is especially important when working in development environments where both package
    managers are used.

### Fix package version problems related to PATH

Source: chatGPT

For example, `@celo/celocli` v3.1.0 is installed, but version is not found:

```sh
$ yarn global add @celo/celocli@latest
# ...
success Installed "@celo/celocli@3.1.0" with binaries:
      - celocli
✨  Done in 50.26s.

$ celocli --version
 ›   Warning: @celo/celocli update available from 3.0.2 to 3.1.0.
@celo/celocli/3.0.2 darwin-arm64 node-v18.16.1

$ yarn global list @celo/celocli
yarn global v1.22.19
info "@celo/celocli@3.1.0" has binaries:
   - celocli
✨  Done in 2.32s.
```

It seems like there's a discrepancy between the version of `celocli` installed via Yarn and the
version that's being invoked in the terminal. This issue can often arise due to path configurations
and how different Node.js versions manage global packages.

Here are a few steps to diagnose and resolve this issue:

#### 1. Check the PATH for Yarn's Global Binaries

-   Yarn installs global binaries in a directory that may not be automatically included in your
    system's PATH. Run `yarn global bin` to find out where Yarn's global binaries are located.
-   Once you have this path, you need to ensure it's included in your PATH environment variable. You
    can do this by adding a line to your `~/.zshrc` or `~/.bashrc` file, like so:
    ```sh
    export PATH="$(yarn global bin):$PATH"
    ```
-   After modifying the file, reload your shell configuration with `source ~/.zshrc` or open a new
    terminal session.

#### 2. Verify the Active Version of `celocli`

-   After updating the PATH, try running `celocli --version` again. If the PATH was the issue, it
    should now correctly show version 3.1.0.

#### 3. Check for Multiple Installations

-   It's possible that `celocli` is installed both via npm and Yarn, leading to version conflicts.
    You can check this by running `npm list -g @celo/celocli` and `yarn global list @celo/celocli`
    to see if the package is installed with both package managers.
-   If it's installed with npm as well, you might consider uninstalling the npm version to avoid
    confusion. Use `npm uninstall -g @celo/celocli` to remove the npm-installed version.

#### 4. Use nvm to Manage Node Versions

-   Since you're using nvm, ensure you're in the correct Node.js version where you installed the new
    `celocli`. Use `nvm use 18.16.1` or the version you prefer, then try running `celocli --version`
    again.

#### 5. Clear Any Caching

-   Sometimes, old versions of binaries can be cached in your shell. Clearing your shell's cache can
    help. You can do this by running `hash -r` in your terminal.

#### 6. Reinstall `celocli`

-   If none of the above solutions work, consider uninstalling `celocli` and then reinstalling it.
    First, uninstall it using Yarn: `yarn global remove @celo/celocli`. Then, reinstall it:
    `yarn global add @celo/celocli@latest`.
