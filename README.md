# NarraLeaf Dictionaries

Spelling word lists for [NarraLeaf Studio](https://github.com/NarraLeaf/NarraLeaf-Studio).

Studio reads `index.json` from this repository, shows what is on offer with its size and
licence, and downloads a list only when an author asks for one. Nothing here is bundled into
Studio, and Studio never fetches anything from here on its own.

## What a dictionary is

A gzipped UTF-8 text file, one word per line, sorted. The forms are **expanded**: `walk`,
`walks`, `walked` and `walking` are each their own line, because Studio checks by lookup and
does not apply affix rules.

A list covering a language should carry every spelling that language accepts. `en` holds both
`colour` and `color`; marking either would be wrong for half the people writing in English.

Chinese and Japanese are not written with spaces, so Studio segments a run against the list
instead of looking single tokens up. Those lists therefore need their single-character words
(你, 的, 是) as entries of their own, or every sentence would segment into nothing.

## `index.json`

    {
        "formatVersion": 1,
        "repository": "https://github.com/NarraLeaf/Dictionaries",
        "dictionaries": [
            {
                "code": "en",                      // language tag, and the cache filename
                "name": "English",                 // shown to the author
                "bytes": 269055,                   // compressed size, so a download can be described first
                "license": "SCOWL (BSD-style, attribution required)",
                "sha256": "24acf011…",             // of the gzipped bytes, checked before anything is written
                "download": "https://…/en.txt.gz", // absolute, https only
                "words": 104433                    // optional, a rough sense of coverage
            }
        ]
    }

Studio validates every field, refuses anything that is not `https:`, refuses the size twice
(the declared length and the actual bytes), and discards a download whose digest does not
match. An entry that omits its licence is dropped rather than shown.

## Adding a language

1. Build the expanded list and gzip it.
2. Add the file and an `index.json` entry with the real size, digest and licence.
3. Open a pull request. Say where the words came from.

## Changing a list that is already published

Republishing a language means new bytes, and a new `sha256` and `bytes` in `index.json`. The
digest is checked against what was just downloaded, so an entry left stale here does not
corrupt a cache - it makes the download fail.

**Nothing is ever fetched in the background.** An author who already has the language keeps the
copy they downloaded until they ask for it again - and the moment they can is *Browse
dictionaries*, where Studio compares the digest it holds against this file and offers a language
whose digest has moved as an update rather than hiding it as installed. So a correction reaches
a new install straight away and an existing one when they next look.

Say what changed in the commit message: it is the only place the difference is recorded.

**Only permissively licensed lists are accepted**, and the licence text ships beside them.
A language whose only available list is copyleft is left out rather than quietly included:
these lists are redistributed to everyone who installs one.

## Sources

- `zh` — the surface forms of [jieba](https://github.com/fxsjy/jieba)'s dictionary, MIT, Copyright
  (c) 2013 Sun Junyi. Notice in `COPYRIGHT-JIEBA`.
- `ja` — the surface forms of [IPAdic](https://github.com/taku910/mecab), Copyright 2000-2003 Nara
  Institute of Science and Technology, redistribution permitted with the notice kept. Notice in
  `COPYRIGHT-IPADIC`.
- `en` — derived from [SCOWL](http://wordlist.aspell.net/) 2020.12.07, sizes 10 to 60,
  English, American and British forms combined. Both the `words` and the `contractions`
  categories: the first release of this list took `words` alone, and since SCOWL keeps `don't`
  and `you've` in a category of their own, a line of dialogue came back almost entirely
  underlined. SCOWL's copyright notice is in `COPYRIGHT-SCOWL`; its permission grant covers the
  output of its scripts, which is what this list is. Copyright 2000-2018 by Kevin Atkinson.
