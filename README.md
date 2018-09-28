# npmsymlink

So you might wonder, what on earth is this and why would I need this? It fixes
a particulary annyoing feature of `npm`, which is the automatic deletion of
**every** `npm link` in your project once a `npm install` is called.

This module, or `cli` provides a solution for that. It has the ability to
automatically (or manually) restore **all** symlinks of your project.

## Installation

As this is a `cli` application, it needs to be installed globally:

```
npm install -g npmsymlink
```

This will install the `npm-link` cli on your system.

## Usage

### `--add`

To add a new link, use the `--add [name]` CLI flag.

```
npm-link --add react
```

This will:

- Automatically call `npm link react` to symlink it.
- Register the package for restore using `npm-link --restore` (or `npm-list --setup`)

### `--remove`

To remove a link, use the `--remove [name]` CLI flag.

```
npm-link --remove react
```

This will:

- Unregister the package for restoration.
- Install the normal version using `npm install` instead.

### `--setup`

Enables fully automated restoration of the symlinks when an `npm install`
happens in your **local** project. So you no longer have to manually `--restore`
after an installation.

```
npm-link --setup
```

This will:

- Add a `postinstall` hook to `node_modules/.hooks`
- The postinstall hook will call `npm-link --restore` each time it's called.

### `--restore`

Used to restore the symlinks that got utterly destroyed by npm after an
`npm install`. This would either be automatically when you used the `--setup`
command, or can be invoked manually.

```
npm-link --restore
```

This will:

- Read all previously stored `npm-link --add <name>` calls.
- Iterate over them, and call `npm link <name>`

## How does it work

The automation, voodoo or black magic of this module is powered by little known
feature of `npm`. You can create `.hooks` file in your `node_modules` folder and
`npm` will **automatically** execute files that are named after the lifecycle
events. So all we need to do is create a `postinstall` hook that will restore
the symlinks.

All added symlinks are automatically saved by this library in the `.hooks` folder
in a dedicated `restore-npm-link.json` file.

## How do I disable `npm-link`

The easiest way is to just nuke your `node_modules` folder and do another install:

```
rm -rf node_modules && npm install .
```

The packages are cached by npm on your disk anyways so re-installation will be
fast. Alternatively you can delete the contents of the `node_modules/.hooks`
folder so the `postinstall` and `restore-npm-link.json` files are deleted.

## License

MIT
