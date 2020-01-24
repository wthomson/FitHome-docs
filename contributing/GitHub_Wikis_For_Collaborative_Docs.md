# Overview
Lurking within every GitHub repo is a separate "special" wiki repo.  Our goal is to use a GitHub wiki for our FitHome documentation.  This requires a few unique operations in order to work.  The main thing is how to handle collaborative documentation.

# Docs Repo
The way we address collaboration is to use a separate repo, FitHome-docs, for editing.  All changes will be made in FitHome-docs.


edit in a separate FitHome-docs repo.  When the project updates, we'll push updates from the docs repository to the wiki on the FitHome project.
## Remotes
The remotes are then:
```
$ git remote -v
origin  https://github.com/BitKnitting/F-docs.git (fetch)
origin  https://github.com/BitKnitting/F-docs.git (push)
wiki    https://github.com/BitKnitting/F.wiki.git (fetch)
wiki    https://github.com/BitKnitting/F.wiki.git (push)
``