---
title: Upgrade z 2. x-Azure Monitor Application Insights Java
description: Upgrade z Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 6e1c7e15ff77fd75ff2fb70a6741ea2dd9a4cab8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040239"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Upgrade z Application Insights Java 2. x SDK

Pokud již v aplikaci používáte sadu Application Insights Java 2. x SDK, není nutné ji odebrat.
Agent Java 3,0 ho detekuje a zachytí a koreluje se všemi vlastními telemetriemi, které posíláte prostřednictvím sady 2. x SDK, při potlačení jakékoli automatické kolekce, kterou provedla sada 2. x SDK, aby nedocházelo k duplicitní telemetrii.

Pokud jste používali agenta Application Insights 2. x, je nutné odebrat `-javaagent:` JVM arg, která odkazovala na agenta 2. x.

Zbývající část tohoto dokumentu popisuje omezení a změny, se kterými se můžete setkat při upgradu z 2. x na 3,0, jakož i některá alternativní řešení, která můžete najít užitečné.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers a TelemetryProcessors

2. x SDK TelemetryInitializers a TelemetryProcessors se nespustí při použití agenta 3,0.
Mnohé z případů použití, které se dřív vyžadovaly, je možné vyřešit v 3,0 konfigurací [vlastních dimenzí](./java-standalone-config.md#custom-dimensions) nebo konfigurací [procesorů telemetrie](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Několik aplikací v jednom JVM

V současné době 3,0 podporuje pouze jeden [připojovací řetězec a název role](./java-standalone-config.md#connection-string-and-role-name) na běžící proces. Konkrétně nemůžete mít k dispozici více webových aplikací Tomcat ve stejném nasazení Tomcat s použitím různých připojovacích řetězců nebo názvů různých rolí.

## <a name="operation-names"></a>Názvy operací

Názvy operací v 3,0 se změnily tak, aby všeobecně poskytovaly lepší agregované zobrazení na portálu Application Insights U/X.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Názvy operací v 3,0":::

U některých aplikací však můžete přesto preferovat agregované zobrazení v/X, které bylo poskytnuto názvy předchozí operace. v takovém případě můžete k replikaci předchozího chování použít funkci [procesory telemetrie](./java-standalone-telemetry-processors.md) (Preview) v 3,0.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Prefixujte název operace metodou http ( `GET` , `POST` atd.).

V sadě 2. x SDK byly názvy operací předponou metody HTTP ( `GET` , `POST` atd.), např.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Názvy operací s předponou metody http":::

Fragment kódu níže konfiguruje 3 procesory telemetrie, které se kombinují k replikaci předchozího chování.
Procesor telemetrie provádí následující akce (v uvedeném pořadí):

1. První procesor telemetrie je procesor s rozsahem (je typu `span` ), což znamená, že platí pro `requests` a `dependencies` .

   Bude odpovídat jakémukoli rozsahu, který má atribut s názvem `http.method` a má název rozpětí, který začíná na `/` .

   Pak se tento název rozbalí do atributu s názvem `tempName` .

2. Druhý procesor telemetrie je také procesor s rozsahem.

   Bude odpovídat jakémukoli rozsahu, který má atribut s názvem `tempName` .

   Pak aktualizuje název rozsahu zřetězením dvou atributů `http.method` a `tempName` oddělených mezerou.

3. Poslední procesor telemetrie je procesor atributů (je typu `attribute` ), což znamená, že se vztahuje na všechny telemetrie, které mají atributy ( `requests` aktuálně `dependencies` a `traces` ).

   Bude odpovídat jakékoli telemetrie s atributem s názvem `tempName` .

   Pak odstraní atribut s názvem `tempName` , aby se nenahlásil jako vlastní dimenze.

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

### <a name="set-the-operation-name-to-the-full-path"></a>Nastavte název operace na úplnou cestu.

V sadě 2. x SDK v některých případech také názvy operací obsahují úplnou cestu, např.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Názvy operací s úplnou cestou":::

Fragment kódu níže nakonfiguruje 4 procesory telemetrie, které se kombinují k replikaci předchozího chování.
Procesor telemetrie provádí následující akce (v uvedeném pořadí):

1. První procesor telemetrie je procesor s rozsahem (je typu `span` ), což znamená, že platí pro `requests` a `dependencies` .

   Bude odpovídat jakémukoli rozsahu, který má atribut s názvem `http.url` .

   Pak aktualizuje název rozsahu `http.url` hodnotou atributu.

   To by bylo na konci, s výjimkou toho, že `http.url` vypadá jako `http://host:port/path` a je pravděpodobný, že budete chtít jenom tuto `/path` část.

2. Druhý procesor telemetrie je také procesor s rozsahem.

   Bude odpovídat jakémukoli rozsahu, který má atribut s názvem `http.url` (jinými slovy, libovolný rozsah, který odpovídá prvnímu procesoru).

   Pak extrahuje část cesty názvu rozpětí do atributu s názvem `tempName` .

3. Třetí procesor telemetrie je také procesor s rozsahem.

   Bude odpovídat jakémukoli rozsahu, který má atribut s názvem `tempPath` .

   Pak bude aktualizovat název rozsahu z atributu `tempPath` .

4. Poslední procesor telemetrie je procesor atributů (je typu `attribute` ), což znamená, že se vztahuje na všechny telemetrie, které mají atributy ( `requests` aktuálně `dependencies` a `traces` ).

   Bude odpovídat jakékoli telemetrie s atributem s názvem `tempPath` .

   Pak odstraní atribut s názvem `tempPath` , aby se nenahlásil jako vlastní dimenze.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
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

## <a name="dependency-names"></a>Názvy závislostí

Také se změnily názvy závislostí ve 3,0 a obecně poskytovaly lepší agregované zobrazení na portálu Application Insights U/X.

Pro některé aplikace můžete znovu preferovat agregované zobrazení ve U/X, které bylo zadáno v předchozích názvech závislostí. v takovém případě můžete použít podobné techniky jako výše k replikaci předchozího chování.

## <a name="operation-name-on-dependencies"></a>Název operace u závislostí

V předchozích verzích sady 2. x SDK byl název operace z telemetrie požadavků také nastaven v telemetrie závislostí.
Application Insights Java 3,0 již neplní název operace v telemetrie závislostí.
Pokud chcete zobrazit název operace pro požadavek, který je nadřazeným prvkem telemetrie závislostí, můžete zapsat dotaz Kusto (protokoly) pro připojení z tabulky závislostí do tabulky požadavků, např.

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>2. Přidání přihlašování k sadě SDK pro sady x

Agent 3,0 [automaticky shromáždí protokolování](./java-standalone-config.md#auto-collected-logging) bez nutnosti konfigurovat žádné připojovací objekty pro protokolování.
Pokud používáte 2. připojení sady SDK pro přihlašování k sadě SDK, je možné je odebrat, protože ho agent 3,0 přesto potlačí.

## <a name="2x-sdk-spring-boot-starter"></a>2. x SDK jaře Boot Starter

Není k dispozici žádný 3,0 pružinového startu Starter.
Nastavení a konfigurace agenta 3,0 postupují podle stejných [jednoduchých kroků](./java-in-process-agent.md#quickstart) bez ohledu na to, jestli používáte pružinové spouštění nebo ne.

Při upgradu ze sady 2. x SDK jaře Boot Starter si všimněte, že název cloudové role už nebude ve výchozím nastavení nastaven na `spring.application.name` .
V [dokumentaci ke konfiguraci 3,0](./java-standalone-config.md#cloud-role-name) najdete nastavení názvu cloudové role v 3,0 prostřednictvím konfigurace JSON nebo proměnné prostředí.
