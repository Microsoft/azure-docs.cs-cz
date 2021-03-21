---
title: Příklady procesorů telemetrie – Azure Monitor Application Insights pro Java
description: Prozkoumejte příklady, které ukazují procesory telemetrie v Azure Monitor Application Insights pro jazyk Java.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734583"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Příklady procesorů telemetrie – Azure Monitor Application Insights pro Java

Tento článek popisuje příklady procesorů telemetrie v Application Insights pro jazyk Java. Najdete ukázky pro zahrnutí a vyloučení konfigurací. Také najdete ukázky pro procesory atributů a rozsah procesorů.
## <a name="include-and-exclude-samples"></a>Zahrnutí a vyloučení ukázek

V této části se dozvíte, jak zahrnout a vyloučit rozsahy. Uvidíte také, jak vyloučit vícenásobné rozsahy a použít selektivní zpracování.
### <a name="include-spans"></a>Zahrnout rozsahy

V této části se dozvíte, jak zahrnout rozsahy pro procesor atributů. Rozsahy, které neodpovídají vlastnostem, nejsou zpracovány procesorem.

Shoda vyžaduje, aby název rozsahu byl roven `spanA` nebo `spanB` . 

Tyto rozsahy odpovídají vloženým vlastnostem a používají se akce procesoru:
* Název Span1: atributy "span": {ENV: dev, test_request: 123, credit_card: 1234}
* Název Span2: atributy spanB: {ENV: dev, test_request: false}
* Název Span3: atributy "span": {ENV: 1, test_request: dev, credit_card: 1234}

Tento rozsah neodpovídá vlastnostem include a akce procesoru nejsou aplikovány:
* Název Span4: atributy spanC: {ENV: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>Vyloučit rozsahy

Tato část ukazuje, jak vyloučit rozsahy pro procesor atributů. Rozsahy odpovídající vlastnostem nezpracovává tento procesor.

Shoda vyžaduje, aby název rozsahu byl roven `spanA` nebo `spanB` .

Následující rozsahy odpovídají vlastnostem Exclude a akce procesoru nejsou aplikovány:
* Název Span1: atributy "span": {ENV: dev, test_request: 123, credit_card: 1234}
* Název Span2: atributy spanB: {ENV: dev, test_request: false}
* Název Span3: atributy "span": {ENV: 1, test_request: dev, credit_card: 1234}

Tento rozsah neodpovídá vlastnostem Exclude a používají se akce procesoru:
* Název Span4: atributy spanC: {ENV: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>Vyloučit rozsahy pomocí více kritérií

Tato část ukazuje, jak vyloučit rozsahy pro procesor atributů. Rozsahy odpovídající vlastnostem nezpracovává tento procesor.

Shoda vyžaduje splnění následujících podmínek:
* Atribut (například `env` nebo `dev` ) musí existovat v rozsahu.
* Rozpětí musí mít atribut, který má klíč `test_request` .

Následující rozsahy odpovídají vlastnostem Exclude a akce procesoru nejsou aplikovány.
* Název Span1: atributy spanB: {ENV: dev, test_request: 123, credit_card: 1234}
* Název Span2: atributy "span": {ENV: dev, test_request: false}

Následující rozsah se neshoduje s vlastnostmi vyloučení a používají se akce procesoru:
* Název Span3: atributy spanB: {ENV: 1, test_request: dev, credit_card: 1234}
* Název Span4: atributy spanC: {ENV: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>Selektivní zpracování

V této části se dozvíte, jak zadat sadu vlastností rozpětí, které určují, které z nich se má použít pro tento procesor. Vlastnosti include označují, které rozsahy by měly být zpracovány. Vlastnosti vyloučení odfiltrují rozsahy, které by neměly být zpracovány.

V následující konfiguraci se tyto rozsahy shodují s vlastnostmi a používají se akce procesoru:

* Název Span1: atributy spanB: {ENV: produkční, test_request: 123, credit_card: 1234, redact_trace: false}
* Název Span2: atributy "span": {ENV: staging, test_request: false, redact_trace: true}

Tyto rozsahy se neshodují s vlastnostmi zahrnutí a akce procesoru nejsou aplikovány:
* Název Span3: atributy spanB: {ENV: produkční, test_request: true, credit_card: 1234, redact_trace: false}
* Název Span4: atributy spanC: {ENV: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```
## <a name="attribute-processor-samples"></a>Ukázky procesorů atributů

### <a name="insert"></a>Vložit

Následující ukázka vloží nový atribut `{"attribute1": "attributeValue1"}` do rozsahů, kde klíč `attribute1` neexistuje.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Vložit z jiného klíče

Následující příklad používá hodnotu z atributu `anotherkey` pro vložení nového atributu `{"newKey": "<value from attribute anotherkey>"}` do rozsahů, kde klíč `newKey` neexistuje. Pokud atribut `anotherkey` neexistuje, žádný nový atribut není vložen do rozsahů.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Aktualizace

Následující příklad aktualizuje atribut na `{"db.secret": "redacted"}` . Aktualizuje atribut `boo` pomocí hodnoty z atributu `foo` . Rozsahy, které nemají atribut, se `boo` nezmění.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Odstranit

Následující příklad ukazuje, jak odstranit atribut, který má klíč `credit_card` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hodnoty hash

Následující příklad ukazuje, jak hash hodnoty stávajících atributů.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extrahovat

Následující příklad ukazuje, jak použít regulární výraz (Regex) k vytvoření nových atributů na základě hodnoty jiného atributu.
Například, `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` jsou vloženy následující atributy:
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. URL: *bez* změny

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

Následující příklad ukazuje, jak zpracovat rozsahy, které mají název rozpětí, který odpovídá vzorům regulárního výrazu.
Tento procesor odebere `token` atribut. Zařadí `password` atribut v rámci rozsahů, kde se shoduje název rozsahu `auth.*` a kde se název rozsahu neshoduje `login.*` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>Vzorky procesoru span

### <a name="name-a-span"></a>Pojmenování rozsahu

Následující příklad určuje hodnoty atributů `db.svc` , `operation` a `id` . Vytvoří nový název rozpětí pomocí těchto atributů v tomto pořadí, které jsou odděleny hodnotou `::` .
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name"></a>Extrakce atributů z názvu rozpětí

Řekněme, že název vstupního rozsahu je `/api/v1/document/12345678/update` . Následující ukázka má za následek název výstupního rozsahu `/api/v1/document/{documentId}/update` . Přidá nový atribut `documentId=12345678` do rozsahu.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Extrakce atributů z názvu rozpětí pomocí zahrnutí a vyloučení

Následující příklad ukazuje, jak změnit název rozsahu na `{operation_website}` . Přidá atribut s klíčovým `operation_website` a hodnotou, `{oldSpanName}` Pokud má rozsah následující vlastnosti:
- Název rozpětí obsahuje `/` libovolné místo v řetězci.
- Název rozpětí není `donot/change` .
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```
