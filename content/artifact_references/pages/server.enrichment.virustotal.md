---
title: Server.Enrichment.Virustotal
hidden: true
tags: [Server Artifact]
---

Submit a file hash to Virustotal for details. Default Public API restriction is 4 requests/min.

This artifact can be called from within another artifact (such as one looking for files) to enrich the data made available by that artifact.

Ex.

  `SELECT * from Artifact.Server.Enrichment.Virustotal(Hash=$YOURHASH)`

`TO-DO`: Implement a timer to spread out requests


```yaml
name: Server.Enrichment.Virustotal
author: Wes Lambert -- @therealwlambert
description: |
  Submit a file hash to Virustotal for details. Default Public API restriction is 4 requests/min.

  This artifact can be called from within another artifact (such as one looking for files) to enrich the data made available by that artifact.

  Ex.

    `SELECT * from Artifact.Server.Enrichment.Virustotal(Hash=$YOURHASH)`

  `TO-DO`: Implement a timer to spread out requests

type: SERVER

parameters:
    - name: Hash
      type: string
      description: The file hash to submit to Hybrid Analysis (MD5, SHA1, SHA256).
      default:

    - name: VirustotalKey
      type: string
      description: API key for Virustotal. Leave blank here if using server metadata store.
      default:

sources:
  - query: |
        LET Creds = if(
           condition=VirustotalKey,
           then=VirustotalKey,
           else=server_metadata().VirustotalKey)

        LET URL <= 'https://www.virustotal.com/api/v3/files/' + Hash

        LET Data = SELECT parse_json(data=Content) AS VTData
        FROM http_client(url=URL, headers=dict(`x-apikey`=Creds))

        SELECT format(format='%v/%v',
             args=[VTData.data.attributes.last_analysis_stats.malicious,
                   VTData.data.attributes.last_analysis_stats.malicious +
                   VTData.data.attributes.last_analysis_stats.undetected]) As VTRating,
            timestamp(epoch=VTData.data.attributes.first_seen_itw_date) AS FirstSeen,
            timestamp(epoch=VTData.data.attributes.first_submission_date) AS FirstSubmitted,
            timestamp(epoch=VTData.data.attributes.last_analysis_date) AS LastAnalysis,
            VTData.data.attributes.crowdsourced_yara_results AS YARAResults,
            VTData AS _Data
        FROM Data

```
