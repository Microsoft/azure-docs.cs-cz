---
title: Přepsání vzorkování (Preview) – Azure Monitor Application Insights pro Java
description: Naučte se konfigurovat přepsání vzorkování v Azure Monitor Application Insights pro Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 17979bd548ca0d7b704ebdeb4d060bf35973b319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024142"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Přepsání vzorkování (Preview) – Azure Monitor Application Insights pro Java

> [!NOTE]
> Funkce přepsání vzorkování je ve verzi Preview, počínaje od 3.0.3-BETA. 2.

Přepsání vzorkování umožňují přepsat [výchozí procento vzorkování](./java-standalone-config.md#sampling), například:
 * Nastavte procento vzorkování na 0 (nebo malou hodnotu) pro kontroly stavu s vysokou úrovní šumu.
 * Nastavte procento vzorkování na 0 (nebo malou hodnotu) pro volání závislostí s vysokou úrovní šumu.
 * Nastavte procento vzorkování na 100 pro důležitý typ žádosti (např.), přestože `/login` máte výchozí vzorkování nakonfigurované na něco menšího.

## <a name="terminology"></a>Terminologie

Než se seznámíte s přepsáním vzorkování, měli byste pochopit *Rozsah* období. Rozpětí je obecný termín pro:

* Příchozí požadavek.
* Odchozí závislost (například vzdálené volání jiné služby).
* Závislost v rámci procesu (například práce prováděná dílčími součástmi služby).

Pro přepsání vzorkování jsou tyto komponenty pro rozsahy důležité:

* Atributy

Atributy span reprezentují standardní i vlastní vlastnosti daného požadavku nebo závislosti.

## <a name="getting-started"></a>Začínáme

Začněte tím, že vytvoříte konfigurační soubor s názvem *applicationinsights.jsv*. Uložte ho do stejného adresáře jako *ApplicationInsights-agent- \* . jar*. Použijte následující šablonu.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Jak to funguje

Po spuštění rozsahu jsou atributy přítomné v rozpětí v daném čase použity ke kontrole, zda se neshodují některé přepsání vzorkování.

Pokud se jeden z vzorkování shodují, pak se jeho procento vzorkování použije k rozhodnutí, zda se má vzorek rozsahu nebo ne.

Je použito pouze první přepsání vzorkování, které odpovídá.

Pokud se neshodují žádné přepisy vzorkování:

* Pokud se jedná o první rozpětí v trasování, použije se [výchozí procento vzorkování](./java-standalone-config.md#sampling) .
* Pokud se nejedná o první rozpětí v trasování, použije se nadřazené rozhodnutí o vzorkování.

> [!IMPORTANT]
> V případě, že rozhodnutí nebylo shromážděno, pak nebudou shromažďována žádná mezimnožina bez ohledu na to, zda existují přepsání vzorkování, která odpovídají mezimnožině daného rozpětí.
> Toto chování je nezbytné, protože jinak přerušená trasování budou mít za následek shromažďování podřízených rozsahů, ale jejich nadřazených rozsahů, které nebyly shromažďovány.

> [!NOTE]
> Rozhodnutí o vzorkování vychází z algoritmu hash traceId (označovaného také jako operationId) k číslu mezi 0 a 100 a tato hodnota hash je pak porovnána s procentem vzorkování.
> Vzhledem k tomu, že všechny rozsahy v daném trasování budou mít stejný traceId, budou mít stejnou hodnotu hash, takže rozhodnutí o vzorkování bude v celém trasování konzistentní.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Příklad: potlačit shromažďování telemetrie pro kontroly stavu

Tím se potlačí shromažďování telemetrie pro všechny požadavky na `/health-checks` .

Tím se také potlačí shromažďování všech podřízených rozsahů (závislostí), které by se za normálních podmínek shromáždily `/health-checks` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Příklad: potlačit shromažďování telemetrie pro volání závislosti s vysokou úrovní šumu

Tím se potlačí shromažďování telemetrie pro všechna `GET my-noisy-key` volání Redis.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Příklad: shromáždění 100% telemetrie pro důležitý typ žádosti

Shromáždí se 100% telemetrie pro `/login` .

Vzhledem k tomu, že u podřízených rozsahů (závislosti) se jedná o rozhodnutí o vzorkování nadřazeného objektu (neexistují žádné přepisy vzorkování pro tento počet podřízených položek), budou shromažďovány i pro všechny požadavky/Login.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Společné atributy span

V této části jsou uvedeny některé běžné atributy rozsahu, které mohou být použity při vzorkování přepsání.

### <a name="http-spans"></a>Rozsahy HTTP

| Atribut  | Typ | Description | 
|---|---|---|
| `http.method` | řetězec | Metoda požadavku HTTP|
| `http.url` | řetězec | Úplná adresa URL požadavku HTTP ve formuláři `scheme://host[:port]/path?query[#fragment]` . Fragment není obvykle přenášen přes protokol HTTP. Pokud je ale tento fragment známý, měl by obsahovat.|
| `http.status_code` | číslo | [Stavový kód odpovědi HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | řetězec | Typ protokolu HTTP. |
| `http.user_agent` | řetězec | Hodnota záhlaví [User-agentu protokolu HTTP](https://tools.ietf.org/html/rfc7231#section-5.5.3) odeslaného klientem |

### <a name="jdbc-spans"></a>JDBC rozpětí

| Atribut  | Typ | Description  |
|---|---|---|
| `db.system` | řetězec | Identifikátor používaného produktu systému správy databáze (DBMS). |
| `db.connection_string` | řetězec | Připojovací řetězec použitý k připojení k databázi. Doporučuje se odebrat vložené přihlašovací údaje.|
| `db.user` | řetězec | Uživatelské jméno pro přístup k databázi. |
| `db.name` | řetězec | Řetězec použitý k nahlášení názvu databáze, ke které se přistupovalo. Pro příkazy, které přepínají databázi, by měl být tento řetězec nastaven na cílovou databázi, i když příkaz neproběhne úspěšně.|
| `db.statement` | řetězec | Příkaz databáze, který se spouští.|
