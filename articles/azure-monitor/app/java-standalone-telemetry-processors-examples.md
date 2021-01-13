---
title: Příklady procesorů telemetrie – Azure Monitor Application Insights pro Java
description: Příklady ilustrující procesory telemetrie v Azure Monitor Application Insights pro jazyk Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: b9ad5347e146fc94b513180c591b00c4f449619f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146437"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Příklady procesorů telemetrie – Azure Monitor Application Insights pro Java

## <a name="includeexclude-samples"></a>Zahrnout/vyloučit ukázky

### <a name="1-include-spans"></a>1. zahrnout rozsahy

Následující znázorňuje, včetně rozsahů pro tento procesor atributů. Tento procesor nezpracovává žádné jiné rozsahy, které neodpovídají vlastnostem.

Následující podmínky jsou splněné pro shodu:
* Název rozsahu musí být rovný "span" nebo "spanB". 

Existují následující možnosti, které odpovídají vloženým vlastnostem a jsou aplikovány akce procesoru.
* Název Span1: atributy "span": {ENV: dev, test_request: 123, credit_card: 1234}
* Název Span2: atributy spanB: {ENV: dev, test_request: false}
* Název Span3: atributy "span": {ENV: 1, test_request: dev, credit_card: 1234}

Následující rozsahy se neshodují s vloženými vlastnostmi a akce procesoru se neaplikují.
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

### <a name="2-exclude-spans"></a>2. vyloučit rozsahy

Následující ukázky demonstrují vyloučení rozpětí pro tento procesor atributů. Tento procesor nezpracovává všechny rozsahy, které odpovídají vlastnostem.

Následující podmínky jsou splněné pro shodu:
* Název rozsahu musí být rovný "span" nebo "spanB". 

Níže jsou ty, které odpovídají vlastnostem Exclude, a akce procesoru se neaplikují.
* Název Span1: atributy "span": {ENV: dev, test_request: 123, credit_card: 1234}
* Název Span2: atributy spanB: {ENV: dev, test_request: false}
* Název Span3: atributy "span": {ENV: 1, test_request: dev, credit_card: 1234}

Následující rozsahy se neshodují s vlastnostmi Exclude a jsou aplikovány akce procesoru.
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

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti rozsahy

Následující ukázky demonstrují vyloučení rozpětí pro tento procesor atributů. Tento procesor nezpracovává všechny rozsahy, které odpovídají vlastnostem.

Následující podmínky jsou splněné pro shodu:
* Atribut (' ENV ', ' dev ') musí existovat v rozsahu pro porovnávání.
* Pokud existuje atribut s klíčem "test_request" v rozsahu, existuje shoda.

Níže jsou ty, které odpovídají vlastnostem Exclude, a akce procesoru se neaplikují.
* Název Span1: atributy spanB: {ENV: dev, test_request: 123, credit_card: 1234}
* Název Span2: atributy "span": {ENV: dev, test_request: false}

Následující rozsahy se neshodují s vlastnostmi Exclude a jsou aplikovány akce procesoru.
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

### <a name="4-selective-processing"></a>4. selektivní zpracování

Následující příklad ukazuje, jak zadat sadu vlastností span, které určují, které z nich se má použít pro tento procesor. `include`Z vlastností, které by měly být zahrnuty, a `exclude` vlastnosti jsou dále vyfiltrovány mimo rozsah, které by neměly být zpracovány.

V níže uvedené konfiguraci se shodují tyto vlastnosti a akce procesoru:

* Název Span1: atributy spanB: {ENV: produkční, test_request: 123, credit_card: 1234, redact_trace: false}
* Název Span2: atributy "span": {ENV: staging, test_request: false, redact_trace: true}

Následující rozsahy se neshodují s vloženými vlastnostmi a akcemi procesoru nejsou aplikovány:
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

Následující kód vloží nový atribut {"attribute1": "attributeValue1"} pro rozsah, kde klíč "attribute1" neexistuje.

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

Následující hodnota používá hodnotu z atributu "anotherkey" pro vložení nového atributu {"newKey": "Value z atributu" anotherkey "} pro rozsah, kde klíč" newKey "neexistuje. Pokud atribut ' anotherkey ' neexistuje, žádný nový atribut není vložen do rozsahů.

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

Následující aktualizuje atribut na {"DB. tajná" ":" redigováné "} a aktualizuje atribut ' Boo ' pomocí hodnoty z atributu ' foo '. Rozsahy bez atributu ' Boo ' se nezmění.

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

Následující příklad ukazuje odstranění atributu s klíčem ' credit_card '.

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

Následující příklad ukazuje hodnoty hash existující hodnoty atributu.

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

Následující příklad ukazuje použití regulárního výrazu pro vytvoření nových atributů na základě hodnoty jiného atributu.
Například předanému http. URL = http://example.com/path?queryParam1=value1 , queryParam2 = hodnota2 budou vloženy následující atributy:
* httpProtocol: http
* httpDomain: example.com
* httpPath: cesta
* httpQueryParams: queryParam1 = Hodnota1, queryParam2 = hodnota2
* hodnota http. URL se nemění.

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

Následující příklad ukazuje, jak zpracovat rozsahy, které mají název rozpětí odpovídající vzorům RegExp.
Tento procesor Odstraní atribut "token" a zařadí atribut "Password" v rozsahu, kde název rozsahu odpovídá "auth \* ". a kde se název span neshoduje s "login \* ".

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

Následující příklad určuje hodnoty atributu "DB. svc", "Operation" a "ID" budou tvořit nový název rozsahu, v tomto pořadí, oddělený hodnotou "::".
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

### <a name="extract-attributes-from-span-name"></a>Extrahovat atributy z rozsahu názvů

Řekněme, že název vstupního rozsahu je/API/v1/Document/12345678/Update. Použití následujících výsledků ve výstupním názvu span/api/v1/document/{documentId}/update přidá nový atribut "documentId" = "12345678" do rozsahu.
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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Extrakce atributů z názvu span pomocí zahrnutí a vyloučení

Následující příklad ukazuje přejmenování názvu rozsahu na "{operation_website}" a přidání atributu {Key: operation_website, Value: oldSpanName}, pokud má rozsah následující vlastnosti:
- Název rozpětí obsahuje znak/kdekoli v řetězci.
- Název rozsahu není "Nepřipojovat/Change".
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