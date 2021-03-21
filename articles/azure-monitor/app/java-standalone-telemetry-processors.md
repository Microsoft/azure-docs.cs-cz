---
title: Procesory telemetrie (Preview) – Azure Monitor Application Insights pro Java
description: Naučte se konfigurovat procesory telemetrie v Azure Monitor Application Insights pro Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 35e53454e5b2c6265082bbedb4a8b60e82df7191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734566"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Procesory telemetrie (Preview) – Azure Monitor Application Insights pro Java

> [!NOTE]
> Funkce telemetrie procesorů je ve verzi Preview.

Agent Java 3,0 pro Application Insights může zpracovat data telemetrie předtím, než se data exportují.

Tady jsou některé případy použití pro procesory telemetrie:
 * Vytvářejte citlivá data.
 * Podmíněně přidejte vlastní dimenze.
 * Aktualizujte název rozsahu, který se používá k agregaci podobné telemetrie v Azure Portal.
 * Odřaďte atributy span pro řízení nákladů na ingestování.

## <a name="terminology"></a>Terminologie

Než se dozvíte o procesorech telemetrie, měli byste pochopit *Rozsah* období. Rozpětí je obecný termín pro:

* Příchozí požadavek.
* Odchozí závislost (například vzdálené volání jiné služby).
* Závislost v rámci procesu (například práce prováděná dílčími součástmi služby).

Pro procesory telemetrie jsou tyto komponenty s rozsahem důležité:

* Name
* Atributy

Název rozsahu je primární zobrazení pro požadavky a závislosti v Azure Portal. Rozsah atributů představuje standardní i vlastní vlastnosti daného požadavku nebo závislosti.

## <a name="telemetry-processor-types"></a>Typy procesorů telemetrie

V současné době dva typy procesorů telemetrie jsou procesory atributů a procesory rozpětí.

Procesor atributů může atributy vložit, aktualizovat, odstranit nebo hash.
Může také použít regulární výraz k extrakci jednoho nebo více nových atributů z existujícího atributu.

Procesor s rozsahem může aktualizovat název telemetrie.
Může také použít regulární výraz pro extrakci jednoho nebo více nových atributů z názvu rozpětí.

> [!NOTE]
> V současné době zpracovávají procesory telemetrie pouze atributy typu String. Nezpracovávají atributy typu Boolean nebo Number.

## <a name="getting-started"></a>Začínáme

Začněte tím, že vytvoříte konfigurační soubor s názvem *applicationinsights.jsv*. Uložte ho do stejného adresáře jako *ApplicationInsights-agent- \* . jar*. Použijte následující šablonu.

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

## <a name="include-criteria-and-exclude-criteria"></a>Zahrnout kritéria a kritéria vyloučení

Procesory atributů i procesory na úrovni span podporují volitelné `include` a `exclude` kritéria.
Procesor se aplikuje jenom na rozsahy, které odpovídají jeho `include` kritériím (Pokud je zadaný) _, a_ neodpovídá jeho `exclude` kritériím (Pokud je k dispozici).

Pokud chcete tuto možnost nakonfigurovat v části `include` nebo `exclude` (nebo v obou), zadejte aspoň jednu `matchType` a buď `spanNames` nebo `attributes` .
Konfigurace include-Exclude umožňuje více než jednu zadanou podmínku.
Všechny zadané podmínky se musí vyhodnotit na hodnotu true, aby bylo možné porovnat výsledek. 

* **Povinné pole**: `matchType` Určuje, jakým způsobem `spanNames` jsou položky polí a `attributes` polí interpretovány. Možné hodnoty jsou `regexp` a `strict` . 

* **Volitelná pole**: 
    * `spanNames` musí odpovídat nejméně jedné z položek. 
    * `attributes` Určuje seznam atributů, které mají být shodné. Všechny tyto atributy se musí přesně shodovat, aby bylo možné porovnat výsledek.
    
> [!NOTE]
> Pokud `include` `exclude` jsou zadány oba a, `include` jsou před `exclude` Zaškrtnutím vlastností zkontrolovány vlastnosti.

### <a name="sample-usage"></a>Ukázkové použití

```json
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
```
Další informace najdete v tématu [Příklady procesoru telemetrie](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Procesor atributů

Procesor atributů mění atributy rozsahu. Může podporovat možnost zahrnout nebo vyloučit rozsahy. Provede seznam akcí, které se provádějí v pořadí, v jakém konfigurační soubor určuje. Procesor podporuje tyto akce:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

`insert`Akce vloží nový atribut z rozsahů, kde klíč ještě neexistuje.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
`insert`Akce vyžaduje následující nastavení:
* `key`
* Buď `value` nebo `fromAttribute`
* `action`: `insert`

### `update`

`update`Akce aktualizuje atribut v poli rozsahy, kde klíč již existuje.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
`update`Akce vyžaduje následující nastavení:
* `key`
* Buď `value` nebo `fromAttribute`
* `action`: `update`


### `delete` 

`delete`Akce odstraní atribut z rozsahu.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
`delete`Akce vyžaduje následující nastavení:
* `key`
* `action`: `delete`

### `hash`

`hash`Hodnoty hash akcí (SHA1) stávající hodnota atributu.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
`hash`Akce vyžaduje následující nastavení:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> Tato `extract` funkce je k dispozici pouze ve verzi 3.0.2 a novější.

`extract`Akce extrahuje hodnoty pomocí pravidla regulárního výrazu ze vstupního klíče do cílových klíčů, které pravidlo určuje. Pokud cílový klíč již existuje, bude přepsán. Tato akce se chová jako nastavení [procesoru span](#extract-attributes-from-the-span-name) `toAttributes` , kde je existující atribut zdrojem.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
`extract`Akce vyžaduje následující nastavení:
* `key`
* `pattern`
* `action`: `extract`

Další informace najdete v tématu [Příklady procesoru telemetrie](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Procesor span

Procesor span upraví název rozsahu nebo atributy rozsahu na základě názvu rozpětí. Může podporovat možnost zahrnout nebo vyloučit rozsahy.

### <a name="name-a-span"></a>Pojmenování rozsahu

`name`Oddíl vyžaduje `fromAttributes` nastavení. Hodnoty z těchto atributů jsou použity k vytvoření nového názvu zřetězeny v pořadí, které určuje konfigurace. Procesor změní název rozpětí pouze v případě, že jsou všechny tyto atributy přítomny v rozsahu.

`separator`Nastavení je volitelné. Toto nastavení je řetězec. Je určen k rozdělení hodnot.
> [!NOTE]
> Pokud při přejmenování dojde k úpravě atributů na procesor atributů, ujistěte se, že je procesor span zadaný po procesoru atributů ve specifikaci kanálu.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-span-name"></a>Extrahovat atributy z názvu rozpětí

V `toAttributes` části jsou uvedeny regulární výrazy, které odpovídají názvu rozpětí proti. Extrahuje atributy založené na podvýrazech.

`rules`Nastavení je povinné. Toto nastavení uvádí pravidla, která se používají k extrakci hodnot atributů z názvu rozpětí. 

Hodnoty v názvu rozpětí jsou nahrazeny názvy extrahovaných atributů. Každé pravidlo v seznamu je vzorový řetězec regulárního výrazu (Regex). 

Tady je způsob, jakým jsou hodnoty nahrazené názvy extrahovaných atributů:

1. Název rozpětí je zkontrolován proti regulárnímu výrazu. 
1. Pokud regulární výraz odpovídá, jsou všechny pojmenované dílčí výrazy regulárního výrazu extrahovány jako atributy. 
1. Extrahované atributy jsou přidány do rozsahu. 
1. Název každého dílčího výrazu se zobrazí jako název atributu. 
1. Část odpovídající podvýrazu se změní na hodnotu atributu. 
1. Odpovídající část názvu rozpětí je nahrazena názvem extrahovaný atribut. Pokud atributy již existují v rozsahu, budou přepsány. 
 
Tento proces se opakuje pro všechna pravidla v pořadí, ve kterém jsou zadaná. Každé další pravidlo funguje na názvu rozsahu, který je výstupem předchozího pravidla.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="common-span-attributes"></a>Společné atributy span

V této části jsou uvedeny některé běžné atributy rozsahu, které mohou procesory telemetrie použít.

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
