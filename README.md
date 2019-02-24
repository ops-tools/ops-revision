# ops-revision

> Copy files and directories adding revisions

ops-revision – is a bash script for versioning files and directories in a straight way by appending the revision to its names.

Think you give `bar` to the script as a source and `foo` as a destination, then it copies it to `foo/bar-4281ddb307e88d6a` for you.

The appended revision calculated from sources relative paths concatenated with its contents then hashed by SHA-256 and cut to a reasonable length.

## Installation

Checkout this repo as a submodule or copy [ops-revision](./ops-revision) script to your project.

## Usage

Running `ops-revision` without parameters or with `--help` switch will show you a short usage message.

```
$ ops-revision
Copy files and directories adding revisions

Usage: ops-revision [options] src [...src] dest

Options:
  -l, --length   Revision length, default: 16
  -s, --symlink  Create symlink from plain to revised dest name
  -n, --no-name  Omit dest name, use only revision
  -e, --sed      Output with sed replace patterns
  -h, --help     Print help and exit
```

### Options

#### -l, --length

The number of symbols the resulting revision will be cut to. The default algorithm for the revision is SHA-256 hash in hex truncated to 16 symbols, so the probability of collision will be 2^32 (2^(128/4)), which means you may face the collision in 1 of 4 billion produced revisions.

#### -s, --symlink

Takes dest name without added revision and makes a symlink to it. If the same symlink already exists, it will be replaced atomically by the new one using `mv` command.

#### -n, --no-name

Produces resulting files without src or dest name, but with revision only.

#### -e, --sed

Writing to stdout `sed` replace patterns formatted as `s/src/dest/g` for piping to sed to make some post-processing, like replacing include directives in other files.

#### -h, --help

Will print usage note with options list then exit.

## Examples

Using ops-revision to make unique names for *.js* and *.css* files and replace occurrences in *index.html* using sed. This is a widely adopted technique to bust the browser's cache on frontend deploy.

```sh
$ ops-revision src/*.{js,css} dest/ -e | sed -f - -i dest/index.html
```

```sh
$ tree dest/
dest/
├── bar-1a0b68e068563945.css
├── foo-9b47986fa9d967e0.js
└── index.html
```

---

Copying a new version of a directory to the destination and atomically switching symlink to it. Pretty common task for replacing static files at the server with zero traffic losses.

```sh
$ ops-revision -s files /var/www/public
files public-ad93a25ac7c73574
```

```sh
$ tree *
files
├── bar
└── foo
var
└── www
    ├── public -> public-ad93a25ac7c73574
    ├── public-ab2211e4bd052657
    │   └── foo
    └── public-ad93a25ac7c73574
        ├── bar
        └── foo
```

## Development

### Code style

Please follow [Shell Style Guide](https://google.github.io/styleguide/shell.xml) by Google when writing new code or making changes.

## License

This software is distributed under the [MIT license](https://github.com/ops-tools/ops-docker/blob/master/LICENSE).
