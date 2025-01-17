---
title: MacOS.System.Plist
hidden: true
tags: [Client Artifact]
---

This artifact collects and/or parses MacOS .plist files.  While simple,
this artifact allows users to specify a .plist glob, and have those plist files
returned for quick review.  If more advanced parsing is desired, the artifact can be copied
and modified.


```yaml
name: MacOS.System.Plist
description: |
  This artifact collects and/or parses MacOS .plist files.  While simple,
  this artifact allows users to specify a .plist glob, and have those plist files
  returned for quick review.  If more advanced parsing is desired, the artifact can be copied
  and modified.

type: CLIENT

author: Wes Lambert - @therealwlambert

precondition: SELECT OS FROM info() WHERE OS =~ 'darwin'

parameters:
  - name: PlistGlob
    default: /Library/Preferences/*.plist

  - name: Upload_File
    default: N
    type: bool

sources:
  - query: |
      SELECT
        OSPath,
        Mtime,
        plist(file=OSPath) AS Content,
        if(condition=Upload_File,
           then=upload(file=OSPath,
                       mtime=Mtime,
                       atime=Atime,
                       ctime=Ctime,
                       btime=Btime)) AS Upload
      FROM glob(globs=PlistGlob)

```
