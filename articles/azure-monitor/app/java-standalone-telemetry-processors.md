---
title: Procesory telemetrie (Preview) – Azure Monitor Application Insights pro Java
description: Jak nakonfigurovat procesory telemetrie v Azure Monitor Application Insights pro Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133170"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Procesory telemetrie (Preview) – Azure Monitor Application Insights pro Java

> [!NOTE]
> Tato funkce je stále ve verzi Preview.

Agent Java 3,0 pro Application Insights nyní má funkce pro zpracování dat telemetrie před exportem dat.

Níže jsou uvedeny některé případy použití procesorů telemetrie:
 * Maskovat citlivá data
 * Podmíněné přidání vlastních dimenzí
 * Aktualizace názvu telemetrie používaného pro agregaci a zobrazení
 * Zrušení nebo filtrování atributů rozsahu pro řízení nákladů na ingestování

## <a name="terminology"></a>Terminologie

Před přechodem na procesory telemetrie je důležité pochopit, co jsou trasování a rozsahy.

### <a name="traces"></a>Trasování

Trasování sleduje průběh jednoho požadavku, který se nazývá a `trace` , jak je zpracovává služby, které tvoří aplikaci. Požadavek může být iniciován uživatelem nebo aplikací. Každá jednotka práce v rámci `trace` se nazývá a `span` ; `trace` je strom rozsahů. A `trace` se skládá z jednoho kořenového rozsahu a libovolného počtu podřízených prvků.

### <a name="span"></a>Kontextové

Rozsahy jsou objekty, které představují práci prováděnou jednotlivými službami nebo komponentami, které jsou součástí požadavku při toku přes systém. `span`Obsahuje `span context` , což je sada globálně jedinečných identifikátorů, které reprezentují jedinečný požadavek, na který je každý rozsah součástí. 

Pokrývá zapouzdření:

* Název rozsahu
* Neproměnlivý `SpanContext` , který jedinečně identifikuje rozsah
* Nadřazený rozsah ve formě `Span` , `SpanContext` nebo null
* Provede `SpanKind`.
* Časové razítko zahájení
* Koncové časové razítko
* [`Attributes`](#attributes)
* Seznam událostí s časovým razítkem
* Úloha `Status`.

Obecně platí, že životní cyklus rozpětí je podobný následujícímu:

* Služba přijala požadavek. Kontext rozpětí je extrahován z hlaviček požadavku, pokud existuje.
* Nový rozsah je vytvořen jako podřízený objekt extrahovaný kontext rozpětí; Pokud žádný neexistuje, vytvoří se nový kořenový rozsah.
* Služba zpracovává požadavek. Další atributy a události jsou přidány do rozsahu, který je užitečný pro porozumění kontextu požadavku, jako je název hostitele počítače, který zpracovává požadavek, nebo identifikátory zákazníka.
* Je možné vytvořit nové rozsahy, které budou představovat práci prováděnou dílčími součástmi služby.
* Když služba provede vzdálené volání jiné služby, aktuální kontext rozsahu je serializován a předán do další služby vložením kontextu rozsahu do hlaviček nebo obálky zprávy.
* Práce prováděná službou se dokončí, úspěšně nebo ne. Stav rozpětí je správně nastaven a rozsah je označen jako dokončený.

### <a name="attributes"></a>Atributy

`Attributes` je seznam nulových nebo více párů klíč-hodnota, které jsou zapouzdřeny v `span` . Atribut musí mít následující vlastnosti:

Klíč atributu, který musí být jiný než null a neprázdný řetězec.
Hodnota atributu, která je buď:
* Primitivní typ: řetězec, logická hodnota, dvojitá přesnost a plovoucí desetinná čárka (IEEE 754-1985) nebo podepsané 64 bitového čísla se znaménkem.
* Pole hodnot primitivního typu. Pole musí být homogenní, tj. nesmí obsahovat hodnoty různých typů. Pro protokoly, které nativně nepodporují hodnoty polí, by tyto hodnoty měly být reprezentované jako řetězce JSON.

## <a name="supported-processors"></a>Podporované procesory:
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

Procesor atributů a procesor rozpětí zpřístupňují možnost poskytnout sadu vlastností rozsahu, který se má porovnat s, aby bylo možné určit, zda má být rozpětí zahrnuto nebo vyloučeno z procesoru telemetrie. Chcete-li konfigurovat tuto možnost, musí být v části `include` a/nebo `exclude` aspoň jedna `matchType` a jedna z `spanNames` nebo `attributes` . Konfigurace zahrnutí/vyloučení je podporovaná tak, aby obsahovala víc než jednu zadanou podmínku. Všechny zadané podmínky se musí vyhodnotit na hodnotu true, aby došlo ke shodě. 

**Povinné pole**: 
* `matchType` Určuje, jakým `spanNames` způsobem `attributes` jsou interpretovány položky a pole. Možné hodnoty jsou `regexp` nebo `strict`. 

**Volitelná pole**: 
* `spanNames` musí odpovídat nejméně jedné z položek. 
* `attributes` Určuje seznam atributů, které mají být porovnány. Všechny tyto atributy se musí přesně shodovat, aby došlo ke shodě.

> [!NOTE]
> Pokud `include` `exclude` jsou zadány oba a, `include` vlastnosti jsou zkontrolovány před `exclude` vlastnostmi.

#### <a name="sample-usage"></a>Ukázka použití

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
Další informace najdete v dokumentaci k [příkladům procesoru telemetrie](./java-standalone-telemetry-processors-examples.md) .

## <a name="attribute-processor"></a>Procesor atributů 

Procesor atributů mění atributy rozsahu. Volitelně podporuje možnost zahrnout/vyloučit rozsahy. Provede seznam akcí, které se provádějí v pořadí zadaném v konfiguračním souboru. Podporované akce:

### `insert`

Vloží nový atribut z rozsahů, kde klíč ještě neexistuje.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
Pro `insert` akci je nutné provést následující akce:
  * `key`
  * jedna z `value` nebo `fromAttribute`
  * `action`:`insert`

### `update`

Aktualizuje atribut v rozpětí, kde klíč existuje.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
Pro `update` akci je nutné provést následující akce:
  * `key`
  * jedna z `value` nebo `fromAttribute`
  * `action`:`update`


### `delete` 

Odstraní atribut z rozsahu.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
Pro `delete` akci je nutné provést následující akce:
  * `key`
  * `action`: `delete`

### `hash`

Hash (SHA1) existující hodnota atributu

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
Pro `hash` akci je nutné provést následující akce:
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Tato funkce je pouze v 3.0.1 a novějších.

Extrahuje hodnoty pomocí pravidla regulárního výrazu ze vstupního klíče do cílových klíčů zadaných v pravidle. Pokud cílový klíč již existuje, bude přepsán. Chová se podobně jako u nastavení [procesoru span](#extract-attributes-from-span-name) `toAttributes` s existujícím atributem jako se zdrojem.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
Pro `extract` akci je nutné provést následující akce:
* `key`
* `pattern`
* `action` : `extract`

Další informace najdete v dokumentaci k [příkladům procesoru telemetrie](./java-standalone-telemetry-processors-examples.md) .

## <a name="span-processors"></a>Procesory rozpětí

Procesor span upraví název rozsahu nebo atributy rozsahu na základě názvu rozpětí. Volitelně podporuje možnost zahrnout/vyloučit rozsahy.

### <a name="name-a-span"></a>Pojmenování rozsahu

V části název je nutné zadat následující nastavení:

* `fromAttributes`: Hodnota atributu pro klíče slouží k vytvoření nového názvu v pořadí zadaném v konfiguraci. Aby se procesor mohl přejmenovat, musí být zadaný v rozsahu všech klíčů atributů.

Volitelně můžete nakonfigurovat následující nastavení:

* `separator`: Řetězec, který se zadá, se použije k rozdělení hodnot.
> [!NOTE]
> Pokud je přejmenování závislé na atributech, které jsou upravovány procesorem atributů, zajistěte, aby byl procesor rozsahu určen po procesoru atributů ve specifikaci kanálu.

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

### <a name="extract-attributes-from-span-name"></a>Extrahovat atributy z rozsahu názvů

Převezme seznam regulárních výrazů, které odpovídají názvu rozpětí proti a extrakci atributů z něj na základě dílčích výrazů. Musí být zadány v `toAttributes` části.

Vyžadují se následující nastavení:

`rules` : Seznam pravidel pro extrakci hodnot atributů z rozsahu názvu. Hodnoty v názvu rozpětí jsou nahrazeny názvy extrahovaných atributů. Každé pravidlo v seznamu je řetězec vzorového regulárního výrazu. Název rozpětí je kontrolován proti regulárnímu výrazu. Pokud regulární výraz odpovídá, jsou všechny pojmenované dílčí výrazy regulárního výrazu extrahovány jako atributy a přidány do rozsahu. Název každého dílčího výrazu se změní na název atributu a část odpovídající podvýrazu se změní na hodnotu atributu. Odpovídající část názvu rozpětí je nahrazena názvem extrahovaný atribut. Pokud atributy již v rozsahu existují, budou přepsány. Tento proces se opakuje pro všechna pravidla v pořadí, ve kterém jsou zadaná. Každé následné pravidlo funguje na názvu rozsahu, který je výstupem po zpracování předchozího pravidla.

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

## <a name="list-of-attributes"></a>Seznam atributů

Níže jsou uvedeny některé běžné atributy span, které lze použít v procesorech telemetrie.

### <a name="http-spans"></a>Rozsahy HTTP

| Atribut  | Typ | Description | 
|---|---|---|
| `http.method` | řetězec | Metoda požadavku HTTP|
| `http.url` | řetězec | Úplná adresa URL požadavku HTTP ve formuláři `scheme://host[:port]/path?query[#fragment]` . Fragmenty se většinou neodesílají přes protokol HTTP, ale pokud je známá, měla by být zahrnutá i přesto.|
| `http.status_code` | číslo | [Stavový kód odpovědi HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | řetězec | Druh používaného protokolu HTTP |
| `http.user_agent` | řetězec | Hodnota záhlaví [User-agentu protokolu HTTP](https://tools.ietf.org/html/rfc7231#section-5.5.3) odeslaného klientem |

### <a name="jdbc-spans"></a>JDBC rozpětí

| Atribut  | Typ | Description  |
|---|---|---|
| `db.system` | řetězec | Identifikátor používaného produktu pro systém správy databáze (DBMS). |
| `db.connection_string` | řetězec | Připojovací řetězec použitý k připojení k databázi. Doporučuje se odebrat vložené přihlašovací údaje.|
| `db.user` | řetězec | Uživatelské jméno pro přístup k databázi. |
| `db.name` | řetězec | Tento atribut slouží k hlášení názvu databáze, ke které se přistupovalo. Příkazy, které přepínají databázi, by měly být nastavené na cílovou databázi (i v případě, že příkaz selhává).|
| `db.statement` | řetězec | Prováděný příkaz databáze.|