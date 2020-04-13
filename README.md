# Docker Command: Get Next Release Number

Simply calculate Next Release from any GIT repository, writen in BASH

Its a part of the Docker Command series

## Usage

Execute the following within your repository folder:

- Using Bash: `cd [git-repository-dir]; ./entrypoint.sh`
- Using Docker: `docker run --rm -v $(pwd):/workspace tpbtools/dc-get-next-release-number`
- Using docker-command-launcher: `kd get-next-release-number [git-repository-dir] > CHANGELOG.md`

The function calculate the "Next Tag" of your git repository based on the unreleased commits (a.k.a. the commits that are not covered by any tag) based on [Semver](https://semver.org/) rules and the first wotd of the commit messages.

If there is no tags in the repository, it shows "v0.1.0". In the other cases the funcion show the calculated "Next Releease" tag based on the following rules of the first word of the commits:

- Increase the major number (first number of the release tag) if there is at least one commit with "Breaking:" starting word.
- If there is no "Breaking:" commits, increase the minor number (second number of the release tag) if there is at least a commit with "New:" or "Upgrade:" starting word
- If there is no "Breaking:", "New:" or "Upgrade:" starting wird, increase the patch number (third number of the release tag)

## Considerations

Make the commits on your repository following [Keep Changelog](https://keepachangelog.com/en/1.0.0/) rules and these keywords:

- Breaking - for a backwards-incompatible enhancement.
- New - implemented a new feature.
- Upgrade - for a dependency upgrade.
- Update - for a backwards-compatible enhancement.
- Fix - for a bug fix.
- Build - changes to build process only.
- Docs - changes to documentation only.
- Security - for security skills.
- Deprecated - for deprecated features.
