Bahamut Lagoon Translation Tutorial
===================================

How to get started
making your own translation of *Bahamut Lagoon*
based on Near's English translation patch.

Ingredients
-----------

In order to use Near's translation toolkit,
you'll need a few things:

  - An unmodified copy of the original Japanese release of *Bahamut Lagoon*
      - For legal reasons we can't point you to where to obtain this file,
        but No-Intro's database lists [the hashes] you'll need
        to identify the correct file.
  - The source-code archive from the [translation's homepage]
  - The source-code to the "beat" patching tool,
    from [Near's homepage]
  - A computer with a C++ compiler and a POSIX runtime environment
      - On Ubuntu, you'll need the following packages installed:
          - build-essential
          - libgtk2.0-dev
          - libgtksourceview2.0-dev
          - libcairo2-dev
      - On another Linux or BSD,
        hopefully you can figure out your distro's equivalents
        for those packages.
      - On Windows,
        you'll need MSYS2 installed,
        and a GCC installed inside that
      - On macOS, you'll need Xcode?
  - A (real or virtual) SNES to test your translation-in-progress
      - An emulator like [bsnes] is recommended,
        but Snes9x, Mesen-S, and others are all fine choices
      - A real SNES with a flash-cart like an Everdrive or FX Pak is fine too
      - No ZSNES. Just, no.

[the hashes]: https://datomatic.no-intro.org/index.php?page=show_record&s=49&n=0152
[translation's homepage]: https://near.sh/bahamut-lagoon
[Near's homepage]: https://near.sh/
[bsnes]: https://github.com/bsnes-emu/bsnes/releases

Setting up the environment
--------------------------

Before you start translating,
you'll need to build all the tools in the toolkit.

Because it's a standalone tool,
let's start with beat, the patching tool.
Extract the archive,
and you should get a new directory
named something like `beat_v2`.
Inside that directory,
run the following commands:

    $ make -C beat install
    make: Entering directory 'beat_v2/beat'
    Compiling hiro/hiro.cpp ...
    [...]
    Compiling beat.cpp ...
    Linking out/beat ...
    mkdir -p ~/.local/bin/
    mkdir -p ~/.local/share/applications/
    mkdir -p ~/.local/share/icons/
    cp out/beat ~/.local/bin/beat
    cp resource/beat.desktop ~/.local/share/applications/beat.desktop
    cp resource/beat.png ~/.local/share/icons/beat.png
    make: Leaving directory 'beat_v2/beat'

The `[...]` in the above screenshot
marks where a bunch of harmless errors have been skipped.

To test that beat was installed correctly,
run it:

    $ beat

You should get a new window titled "beat v2",
with options "Apply Patch", "Create Patch", and "Usage Instructions"
down the left-hand side.

Next,
let's get into the actual translation source.
Extract the source archive,
and you should get a new directory
named something like `bahamut-lagoon-en-20201224`
containing the following items:

  - `bahamut` - the actual source code of the translation
  - `bass` - the source code of the assembler
    used to build the translation into a SNES ROM
  - `hiro` - a user-interface library used by the translation tools
  - `nall` - a general-purpose library used by the translation tools

For now, run the following commands
to build and install `bass`, the assembler:

    $ cd bass/
    $ make install
    Compiling bass.cpp ...
    Linking out/bass ...
    cp out/bass ~/.local/bin/bass
    mkdir -p ~/.local/share/bass/architectures/
    cp -R data/architectures/* ~/.local/share/bass/architectures/

To test that it installed correctly
run it:

    $ bass
    bass v17

    usage:
      bass [options] source [source ...]

    options:
      -o target        specify default output filename [overwrite]
      -m target        specify default output filename [modify]
      -d name[=value]  create define with optional value
      -c name[=value]  create constant with optional value
      -strict          upgrade warnings to errors
      -benchmark       benchmark performance

If you get different output,
from either the "build" or "test" steps above,
something has gone badly wrong
and you need to fix it before you continue.

Before we can use the translation tools,
you need to copy the unmodified original *Bahamut Lagoon* rom
into the `bahamut/jp/rom/` directory,
and make sure it's named `bahamut-jp.sfc`.

Now, we can build the actual translation tools:

    $ cd ../bahamut/tools
    $ make
    Compiling hiro/hiro.cpp ...
    [...]
    Compiling base56.cpp ...
    [...]
    Compiling compressor.cpp ...
    Compiling decompressor.cpp ...
    [...]
    Compiling font-encoder.cpp ...
    Compiling font-extractor.cpp ...
    [...]
    Compiling font-kerner.cpp ...
    Compiling list-editor.cpp ...
    Compiling list-encoder.cpp ...
    Compiling list-extractor.cpp ...
    Compiling script-editor.cpp ...
    Compiling script-encoder.cpp ...
    Compiling script-extractor.cpp ...
    Compiling string-scanner.cpp ...
    [...]

The bits marked `[...]` are a lot of warnings that can be safely ignored.

Building a patched ROM
----------------------

To build the English version of the ROM,
run the following commands:

    $ make build
    out/base56
    out/font-encoder
    out/list-encoder
    out/script-encoder
    $ make patch
    Deleting ../en/rom/bahamut-en.bps ...
    cd ../source && bass main.asm
    beat -create:bps ../en/rom/bahamut-en.bps ../jp/rom/bahamut-jp.sfc ../en/rom/bahamut-en.sfc
    patch created successfully

Now you should be able to load the resulting ROM
into your favourite emulator to try it out:

    $ bsnes ../en/rom/bahamut-en.sfc

Note that bsnes may give you an error at this point:

> error: source size mismatch
> 
> Please ensure you are using the correct (headerless) ROM for this patch.

This is because `make patch` builds `bahamut-en.bps` (the patch)
as well as `bahamut-en.sfc` (the pre-patched ROM).
When bsnes sees a patch and a ROM side-by-side,
it tries to apply the patch to the ROM,
not realising that the patch was built *from* the ROM.
It's OK to ignore this error and keep playing the game.

The initial state
-----------------

Once the game loads
and you get to the main menu,
select New Game,
and keep pressing buttons until you get to a blue dialog box
that's completely empty.
Where's the text?

The *Bahamut Lagoon* translation kit
is intended to help you translate into your language
from the original Japanese,
not to edit the English translation.
It includes English translations of items, menus and messages,
but not translations of dialog — those are all left blank.

If you don't know Japanese,
only English and your target language,
you can ask Tom ([@RetroTranslator] on Twitter)
for permission to base your work on his,
and he can send you the English script files.

[@RetroTranslator]: https://twitter.com/RetroTranslator

Editing the script
------------------

Now we know we can build a ROM,
we can start editing the translation.
To edit the first text-boxes that appear in the game,
launch the Script Editor:

    $ ./out/script-editor

The script editor window
is divided into a number of sections:

  - On the left is a list of all the sections of the script,
    "Chapters" for normal interactions between characters
    and "Fields" for interactions in the middle of a battle
  - Each section is divided into "blocks",
    which are generally a single scene,
    with one or more dialog boxes,
    the "first", "previous", "next", and "last" buttons across the top
    let you browse the available blocks,
    and the drop-down list lets you jump to a particular block
  - The top-left panel shows the original Japanese text for this block
  - The top-middle panel shows how the original text
    was displayed by the original game
  - The top-right panel can be used to store
    translator's notes about this block,
    like identifying a particular name as a mythological reference,
    or when a particular line is a setup for or callback to
    to something later/earlier in the game
  - The bottom-left panel is where you type your translation
  - The bottom-middle panel shows how the new text will be displayed in-game
  - The bottom-right panel shows miscellaneous technical information
      - where is the FIRST pointer in the decompressed event code block for this text located?
      - where is the text within the block located?
      - what Y position does the dialogue box appear at?
      - is it on a transparent background or a text box?

To edit the first text in the game,
go to Chapter 00, Block 2 of 40.
This is the "Prologue" title-card in the official translation.
Put whatever text you like in the bottom-left pane.

The first actual dialog
is Chapter 00, Block 3 of 40,
Put some text in that block, too.

(see *Editing Reference* below
for details of formatting characters and codes you can use
in your script text)

When you're done editing the script,
choose "Save and Quit" from the File menu,
then rebuild the patch:

    $ make build
    $ make patch

Now when you load the English ROM
in your emulator of choice,
you should see the text that you entered
appear in-game!

Editing menus, items, and messages
----------------------------------

There's one other editor you should be aware of.
Besides the Script Editor we just looked at,
which lets you translate the main dialog of the game,
there's also the List Editor
which lets you translate menu entries,
item names,
and the like:

    $ ./out/list-editor

Like the Script Editor,
there's a list of categories down the left hand side,
and first/previous/next/last buttons and a drop-down list across the top.
Below that,
we have the Japanese text on the left
and the in-game rendering on the right,
then the English text and in-game rendering.
The big area at the bottom
is for tranlator's notes.

After making changes in the List Editor,
you'll need to choose "Save and Quit" from the File menu,
then rebuild the patch
in order to see the results.

Publishing
----------

When your translation is complete,
you'll want to share it with other people
so they can enjoy it.
It's not legal to give them the pre-patched ROM,
but you can give them a copy of the `en/rom/bahamut-en.bps` file,
which they can use with a tool like beat
to reconstruct your translation from the original Japanese ROM.

Debugging mode
==============

The original game includes a debugging mode,
which allows you to jump between chapters
to easily test your script changes.

To enabled the debugging mode,
search for "enable the debugger"
in `source/main.asm`
and uncomment the following line.

Editing Reference
=================

Formatting
----------

When editing text,
there are some special codes you can use
for formatting and special effects:

  - `<` makes the following text italic
  - `>` makes the following text regular (non-italic)
  - `[` makes the following text yellow
  - `]` makes the following text white
  - `?` is a question-mark glyph whose width matches digit glyphs
  - `^` is a question-mark with a more natural width
  - `-` is a minus glyph whose width matches digit glyphs
  - `~` is a minus glyph with a more natural width

Note that
italics and yellow text can be combined however you like.
The `<` and `>` codes do not need to be matching pairs,
and neither do `[` and `]`.

Keyboard shortcuts
------------------

The following keyboard shortcuts are available:

  - Shift-F5: Jump to the first item of the category
  - F5: Jump to the previous item
  - F8: Jump to the next item
  - Shift-F8: Jump to the last item

TODO
====

Some things that would be handy to include in this documentation:

  - Other formatting codes, like horizontal alignment,
    pauses, etc.
  - What do those numbers in the bottom-right of the Script Editor mean?
  - More specific instructions for Windows
  - Describe how to use the debugging mode.

Suggestions for changes to the translation toolkit,
to make it easier to get started:

  - Some text fields were invisible to me when building with GTK+2.
    Perhaps the default should be GTK+3 instead?
  - Make the default script data contain counters instead of blank text,
    so the game doesn't start with a completely blank screen
  - Add a separate "make rom" target that doesn't build a patch,
    since you don't need it until distribution time,
    and the patch just confuses soft-patching emulators
  - Teach make about all the dependencies between the translation source text
    and binary blobs, so that "make rom" always does the right thing
  - Bundle beat, so we know we have a compatible version
  - Run bass and beat by relative path, so we don't have to `make install` them
    and so we know we're using the correct version
