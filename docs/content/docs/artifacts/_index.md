---
title: "Velociraptor Artifacts"
date: 2023-10-15T00:14:44+10:00
weight: 10
---


You can import the artifacts within this project using a server
artifact. This artifact will automatically import the latest version.

{{% notice "info" %}}

This artifact is already included in the official Velociraptor
release, but the below represents the latest version.

{{% /notice %}}


```yaml
name: Server.Import.CuratedSigma
description: |
  This artifact allows importing curated Sigma rules from
  https://sigma.velocidex.com

  Collect this artifact on the server to automatically import or
  update these artifacts.

type: SERVER

required_permissions:
- SERVER_ADMIN

parameters:
  - name: PackageNames
    type: multichoice
    default: '["Velociraptor Hayabusa Ruleset"]'
    choices:
      - Velociraptor Hayabusa Ruleset
      - Velociraptor Hayabusa Live Detection
      - Velociraptor ChopChopGo Ruleset (Linux)
      - Velociraptor Curated Windows Ruleset

  - name: Prefix
    description: Add this prefix to imported artifacts
    validating_regex: '^[a-zA-Z0-9_.]*$'

sources:
  - query: |
      LET URLlookup = dict(
        `Velociraptor ChopChopGo Ruleset (Linux)`="https://sigma.velocidex.com/Velociraptor-ChopChopGo-Rules.zip",
        `Velociraptor Hayabusa Ruleset`="https://sigma.velocidex.com/Velociraptor-Hayabusa-Rules.zip",
        `Velociraptor Hayabusa Live Detection`="https://sigma.velocidex.com/Velociraptor-Hayabusa-Monitoring.zip")

      SELECT * FROM foreach(row=PackageNames,
                            query={SELECT * FROM
                                Artifact.Server.Import.ArtifactExchange(
                                Prefix=Prefix,
                                ExchangeURL=get(item= URLlookup, member= _value))})
```
