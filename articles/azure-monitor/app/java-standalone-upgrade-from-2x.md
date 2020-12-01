---
title: Upgrade z 2. x-Azure Monitor Application Insights Java
description: Upgrade z Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355044"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgrade z Application Insights Java SDK 2. x

Pokud v aplikaci již používáte Application Insights Java SDK 2. x, není nutné ji odebrat.
Agent Java 3,0 ho detekuje a zachytí a koreluje se všemi vlastními telemetriemi, které posíláte prostřednictvím sady Java SDK 2. x, a přitom potlačí všechny automatické kolekce, které provádí Java SDK 2. x, aby nedocházelo k duplicitní telemetrii.

Pokud jste používali agenta Application Insights 2. x, je nutné odebrat `-javaagent:` JVM arg, která odkazovala na agenta 2. x.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers a TelemetryProcessors

Sada Java SDK 2. x TelemetryInitializers a TelemetryProcessors se při použití agenta 3,0 nespustí.
Mnohé z případů použití, které se dřív vyžadovaly, je možné vyřešit v 3,0 konfigurací [vlastních dimenzí](./java-standalone-config.md#custom-dimensions) nebo konfigurací [procesorů telemetrie](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Několik aplikací v jednom JVM

V současné době 3,0 podporuje pouze jeden [připojovací řetězec a název role](./java-standalone-config.md#connection-string-and-role-name) na běžící proces. Konkrétně nemůžete mít k dispozici více webových aplikací Tomcat ve stejném nasazení Tomcat s použitím různých připojovacích řetězců nebo názvů různých rolí.

## <a name="http-request-telemetry-names"></a>Názvy telemetrie požadavků HTTP

Názvy telemetrie požadavků HTTP v 3,0 se změnily tak, aby všeobecně poskytovaly lepší agregované zobrazení na portálu Application Insights U/X.

U některých aplikací ale můžete přesto preferovat agregované zobrazení v/X, které bylo poskytnuto předchozími názvy telemetrie. v takovém případě můžete použít funkci ve verzi Preview procesorů telemetrie v 3,0 a vrátit se k předchozím názvům.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>Pro předponu názvu telemetrie metodou http ( `GET` , `POST` atd.):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>Nastavení názvu telemetrie na úplnou cestu URL

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
