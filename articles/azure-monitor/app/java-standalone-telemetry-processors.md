---
title: Procesory telemetrie (Preview) – Azure Monitor Application Insights Java
description: Procesory telemetrie pro Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: e3c41a7a9968a7de743f0c513b1f2b194501d0df
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425792"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Procesory telemetrie (Preview) pro Azure Monitor Application Insights Java

> [!NOTE]
> Tato funkce je stále ve verzi Preview.

Agent Java 3,0 pro Application Insights nyní má funkce pro zpracování dat telemetrie před exportem dat.

### <a name="some-use-cases"></a>Některé případy použití:
 * Maskovat citlivá data
 * Podmíněné přidání vlastních dimenzí
 * Aktualizace názvu telemetrie používaného pro agregaci a zobrazení

### <a name="supported-processors"></a>Podporované procesory:
 * Procesor atributů
 * Procesor span

## <a name="to-get-started"></a>Začněte tím, že

Vytvořte konfigurační soubor s názvem `applicationinsights.json` a umístěte jej do stejného adresáře jako `applicationinsights-agent-***.jar` s následující šablonou.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Zahrnout/vyloučit rozsahy

Procesor atributů a procesor rozpětí zpřístupňují možnost poskytnout sadu vlastností rozsahu, který se má shodovat s, abyste zjistili, jestli by se rozpětí mělo zahrnout nebo vyloučit z procesoru. Chcete-li konfigurovat tuto možnost, musí být v části `include` a/nebo `exclude` aspoň jedna `matchType` a jedna z `spanNames` nebo `attributes` . Konfigurace zahrnutí/vyloučení je podporovaná tak, aby obsahovala víc než jednu zadanou podmínku. Všechny zadané podmínky se musí vyhodnotit na hodnotu true, aby došlo ke shodě. 

**Povinné pole** : 
* `matchType` Určuje, jakým `spanNames` způsobem `attributes` jsou interpretovány položky a pole. Možné hodnoty jsou `regexp` nebo `strict`. 

**Volitelná pole** : 
* `spanNames` musí odpovídat nejméně jedné z položek. 
* `attributes` Určuje seznam atributů, které mají být porovnány. Všechny tyto atributy se musí přesně shodovat, aby došlo ke shodě.

> [!NOTE]
> Pokud `include` `exclude` jsou zadány oba a, `include` vlastnosti jsou zkontrolovány před `exclude` vlastnostmi.

#### <a name="sample-usage"></a>Ukázkové použití

Následující příklad ukazuje, jak zadat sadu vlastností span, které určují, které z nich se má použít pro tento procesor. `include`Z vlastností, které by měly být zahrnuty, a `exclude` vlastnosti jsou dále vyfiltrovány mimo rozsah, které by neměly být zpracovány.

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
            "svcA",
            "svcB"
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

U výše uvedených konfigurací se budou používat následující možnosti, které odpovídají vlastnostem a akcím procesoru:

* Název Span1: atributy svcB: {ENV: produkční, test_request: 123, credit_card: 1234, redact_trace: false}

* Název Span2: atributy svcA: {ENV: staging, test_request: false, redact_trace: true}

Následující rozsahy se neshodují s vloženými vlastnostmi a akcemi procesoru nejsou aplikovány:

* Název Span3: atributy svcB: {ENV: produkční, test_request: true, credit_card: 1234, redact_trace: false}

* Název Span4: atributy svcC: {ENV: dev, test_request: false}

## <a name="attribute-processor"></a>Procesor atributů 

Procesor atributů mění atributy rozsahu. Volitelně podporuje možnost zahrnout/vyloučit rozsahy.
Provede seznam akcí, které se provádějí v pořadí zadaném v konfiguračním souboru. Podporované akce:

* `insert` : Vloží nový atribut do rozsahů, kde klíč ještě neexistuje.
* `update` : Aktualizuje atribut v rozpětí, kde klíč existuje.
* `delete` : Odstraní atribut z rozsahu.
* `hash`   : Hash (SHA1) existující hodnota atributu

Pro akce `insert` a `update`
* `key` je povinné
* jedna z `value` nebo `fromAttribute` je povinná.
* `action` je povinné.

Pro `delete` akci,
* `key` je povinné
* `action`: `delete` je povinné.

Pro `hash` akci,
* `key` je povinné
* `action` : `hash` je povinné.

Seznam akcí může být vytvořen pro vytváření bohatých scénářů, jako je například atribut pro vyplňování pozadí, kopírování hodnot do nového klíče a redigování citlivých informací.

#### <a name="sample-usage"></a>Ukázkové použití

Následující příklad ukazuje vložení klíčů/hodnot do rozsahů:

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
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

Následující příklad znázorňuje konfiguraci procesoru tak, aby aktualizoval pouze existující klíče v atributu:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
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

## <a name="span-processors"></a>Procesory rozpětí

Procesor span upraví název rozsahu nebo atributy rozsahu na základě názvu rozpětí. Volitelně podporuje možnost zahrnout/vyloučit rozsahy.

### <a name="name-a-span"></a>Pojmenování rozsahu

V části název je nutné zadat následující nastavení:

* `fromAttributes`: Hodnota atributu pro klíče slouží k vytvoření nového názvu v pořadí zadaném v konfiguraci. Aby se procesor mohl přejmenovat, musí být zadaný v rozsahu všech klíčů atributů.

Volitelně můžete nakonfigurovat následující nastavení:

* `separator`: Řetězec, který se zadá, se použije k rozdělení hodnot.
> [!NOTE]
> Pokud je přejmenování závislé na atributech, které jsou upravovány procesorem atributů, zajistěte, aby byl procesor rozsahu určen po procesoru atributů ve specifikaci kanálu.

#### <a name="sample-usage"></a>Ukázkové použití

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

Převezme seznam regulárních výrazů, které odpovídají názvu rozpětí proti a extrakci atributů z něj na základě dílčích výrazů. Musí být zadány v `toAttributes` části.

Vyžadují se následující nastavení:

`rules` : Seznam pravidel pro extrakci hodnot atributů z rozsahu názvu. Hodnoty v názvu rozpětí jsou nahrazeny názvy extrahovaných atributů. Každé pravidlo v seznamu je řetězec vzorového regulárního výrazu. Název rozpětí je kontrolován proti regulárnímu výrazu. Pokud regulární výraz odpovídá, jsou všechny pojmenované dílčí výrazy regulárního výrazu extrahovány jako atributy a přidány do rozsahu. Název každého dílčího výrazu se změní na název atributu a část odpovídající podvýrazu se změní na hodnotu atributu. Odpovídající část názvu rozpětí je nahrazena názvem extrahovaný atribut. Pokud atributy již v rozsahu existují, budou přepsány. Tento proces se opakuje pro všechna pravidla v pořadí, ve kterém jsou zadaná. Každé následné pravidlo funguje na názvu rozsahu, který je výstupem po zpracování předchozího pravidla.

#### <a name="sample-usage"></a>Ukázkové použití

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