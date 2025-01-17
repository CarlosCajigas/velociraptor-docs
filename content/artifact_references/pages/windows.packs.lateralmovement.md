---
title: Windows.Packs.LateralMovement
hidden: true
tags: [Client Artifact]
---

Detect evidence of lateral movement.


```yaml
name: Windows.Packs.LateralMovement
description: |
  Detect evidence of lateral movement.

precondition: SELECT OS From info() where OS = 'windows'

reference:
  - https://digital-forensics.sans.org/media/SANS_Poster_2018_Hunt_Evil_FINAL.pdf

sources:
  - name: AlternateLogon
    query: |
      SELECT * FROM Artifact.Windows.EventLogs.AlternateLogon()

  - name: WMIC
    query: |
      SELECT * FROM Artifact.Windows.Forensics.Prefetch()
      WHERE Executable =~ "wmic.exe"
  - name: ShimCache
    query: |
      SELECT * FROM Artifact.Windows.Registry.AppCompatCache()
      WHERE Name =~ "wmic.exe"
  - name: BAM
    query: |
      SELECT * FROM Artifact.Windows.Forensics.Bam()
      WHERE Binary =~ "wmic.exe"
  - name: AmCache
    query: |
      SELECT * FROM Artifact.Windows.System.Amcache()
      WHERE Binary =~ "wmic.exe"

```
