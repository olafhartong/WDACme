# WDACme
A WDAC configuration repository with the sole intention of enriching MDE.

Microsoft Defender for Endpoint (MDE) is a pretty nice EDR that provides a ton of telemetry. However, due to bandwith and cost considerations a lot of the telemetry is sampled, in some cases quite severely.
In a lot of cases this sampling is applied in the form of a repetition cap over a certain time frame where it will log the first seen event. Not ideal, but predictable.

However in the case of the DeviceImageLoadEvents, or DLL Load events there is no direct rationale to what the filtering or sampling is applied. It feels very random and therefore quite unreliable to me. 

This repository contains a Windows Defender Application Control (WDAC) configuration that contains a list of files I always want to know about.
MDE also logs these events to the DeviceEvents table and exposes them in the ActionType *AppControlCodeIntegrityPolicyAudited*

Please note this table is also sampled as this configuration snippet shows;
```json
  "capping": {
    "globalCapping": {
      "capping": 500
    },
    "localCapping": [
      {
        "id": "DGPolicyFailureAuditFirstSeen",
        "expirationPeriodInHours": 24,
        "fields": [
          {
            "fieldName": "File Name"
          },
          {
            "fieldName": "Process Name"
          },
          {
            "fieldName": "Status"
          }
        ],
        "capping": 1
      }
    ]
  }
```

This means that a total of 500 events per 24 hours will be logged and 1 distinct event combination of the File Name, Process Name and Status per 24 hours.

**Also important, this configuration is not enforcing the blocks and will only log events.**

### How to use

Obviously every binary file from the internet is trustworthy but make an effort to test it first in a lab.

Place the .cip file in C:\Windows\System32\CodeIntegrity\CiPolicies\Active

There is great documentation on how to roll it out in production here;
https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/windows-defender-application-control-deployment-guide

