bahamut-lagoon-history-kit
==========================

This repository contains
[various snapshots and changelogs](./sources/)
of Near's 2021 translation of
the Super Famicom RPG *Bahamut Lagoon*,
along with a script that converts them into a Git repository.

Before you start
================

To run these scripts, you'll need:

  - A copy of Python 3.5 or higher
  - A copy of [Git](https://git-scm.com/)
    and specifically the `git fast-import` tool

Usage
=====

Running the `bahamut-lagoon-fast-export` script
should produce output that can be piped into `git fast-import`.
Using it looks something like this:

 1. Clone this repository:

        git clone https://github.com/higan-emu/bahamut-lagoon-history-kit/

 2. Make a new repository to contain the new history:

        git init bahamut-lagoon

 3. Import the history into the new repo:

        cd bahamut-lagoon
        ../bahamut-lagoon-history-kit/bahamut-lagoon-fast-export | git fast import
        git reset --hard HEAD

 4. Now the `bahamut-lagoon` repository contains the development history.
