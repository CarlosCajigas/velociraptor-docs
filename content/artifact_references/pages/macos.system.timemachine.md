---
title: MacOS.System.TimeMachine
hidden: true
tags: [Client Artifact]
---

This artifact collects information about MacOS Time Machine backups.


```yaml
name: MacOS.System.TimeMachine
description: |
  This artifact collects information about MacOS Time Machine backups.

type: CLIENT

author: Wes Lambert - @therealwlambert

parameters:
  - name: TimeMachineGlob
    default: /Library/Preferences/com.apple.TimeMachine.plist

sources:
  - query: |
      LET TMPlist = SELECT FullPath FROM glob(globs=TimeMachineGlob)
      LET TMDetails =
            SELECT * FROM foreach(
                row=plist(file=FullPath),
                query={ SELECT
                    plist(file=FullPath).LocalizedDiskImageVolumeName AS VolumeName,
                    plist(file=FullPath).AutoBackup AS AutoBackup,
                    plist(file=FullPath).LastDestinationID AS LastDestination,
                    plist(file=FullPath).HostUUIDs[0] AS HostUUID,
                    plist(file=FullPath).Destinations AS Destinations
                    FROM scope()
                }
            )
      SELECT * FROM foreach(row=TMPlist, query=TMDetails)

```
