# How to Create and Maintain a Tap

Taps are external sources of Homebrew formulae and/or external commands. They
can be created by anyone to provide their own formulae and/or external commands
to any Homebrew user.

## Creating a tap
A tap is usually a Git repository available online, but you can use anything as
long as it’s a protocol that Git understands, or even just a directory with
files in it.
If hosted on GitHub, we recommend that the repository’s name start with
`homebrew-`.

Tap formulae follow the same format as the core’s ones, and can be added at the
repository’s root, or under `Formula` or `HomebrewFormula` subdirectories. We
recommend the latter options because it makes the repository organisation
easier to grasp, and top-level files are not mixed with formulae.

See [homebrew/php](https://github.com/Homebrew/homebrew-php) for an example of
a tap with a `Formula` subdirectory.

### Installing
If it’s on GitHub, users can install any of your formulae with
`brew install user/repo/formula`. Homebrew will automatically add your
`github.com/user/homebrew-repo` tap before installing the formula.
`user/repo/formula` points to the `github.com/user/homebrew-repo/**/formula.rb`
file here.

If they want to get your tap without installing any formula at the same time,
users can add it with the [`brew tap` command](brew-tap.md).

If it’s on GitHub, they can use `brew tap user/repo`, where `user` is your
GitHub username and `homebrew-repo` your repository.

If it’s hosted outside of GitHub, they have to use `brew tap user/repo <URL>`,
where `user` and `repo` will be used to refer to your tap and `<URL>` is your
Git clone URL.

Users can then install your formulae either with `brew install foo` if there’s
no core formula with the same name, or with `brew install user/repo/foo` to
avoid conflicts.

## Maintaining a tap
A tap is just a Git repository so you don’t have to do anything specific when
making modifications, apart from committing and pushing your changes.

### Updating
Once your tap is installed, Homebrew will update it each time a user runs
`brew update`. Outdated formulae will be upgraded when a user runs
`brew upgrade`, like core formulae.

## External commands
You can provide your tap users with custom `brew` commands by adding them in a
`cmd` subdirectory. [Read more on external commands](External-Commands.md).

See [homebrew/aliases](https://github.com/Homebrew/homebrew-aliases) for an
example of a tap with external commands.


## Use Travis CI to test and bottle your formulae

The idea is to use the staged
1) `brew test-bot` on every architecture you cant to support (e.g., linux,
   sierra, high_sierra...)

### Tricks I want to remember when I set up Travis CI

1) when using `brew test-bot`, never use `--ci-pr`, `--ci-master`,
   `--ci-testing` on your mac as it is going to remove everything installed.
   You can still use `brew test-bot` and `brew test-bot --ci-upload`.

### Drawbacks w.r.t. bottles when maintaining a tap relying on core formulae

On the homebrew-core repo, whenever a formula on which some other formulae
are relying on is updated, we also must rebuild the bottles of these dependent
formulae (this is why we have the revision number `_1` after the actual version
number). This seems to be automatically taken care of on the homebrew-core
tap.

On your own tap, this revision bump whenever a dependency bottle gets rebuilt
can lead to broken bottles. This problem has not been solved yet, but:

1) may be we could rely on a cron job that tests the bottles every week to check
   if the bottle still works;
2) or we could subscribe to the changes on the formulae we depend on and
   run a script on a Travis CI build

Both solutions require a lot a thoughts, that is why I did not do anything
about it. I only rely on `gmp` at runtime so maybe it is fine...