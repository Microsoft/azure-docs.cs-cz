---
title: Vysvětlení jazyka Azure IoT Hub dotazování | Microsoft Docs
description: Příručka pro vývojáře – popis dotazovacího jazyka IoT Hub podobného SQL, který se používá k načtení informací o nečinnosti zařízení/modulu a úlohách ze služby IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: 77becbf4777d0668991adcd74b722cd28ac36f03
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031173"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Dotazovací jazyk služby IoT Hub pro dvojčata zařízení a modulů, úlohy a směrování zpráv

IoT Hub poskytuje výkonný jazyk podobný SQL, který načte informace týkající se [nevláken zařízení](iot-hub-devguide-device-twins.md), [vláken modulů](iot-hub-devguide-module-twins.md), [úloh](iot-hub-devguide-jobs.md)a [směrování zpráv](iot-hub-devguide-messages-d2c.md). Tento článek uvádí:

* Úvod k hlavním funkcím dotazovacího jazyka IoT Hub a
* Podrobný popis jazyka. Podrobnosti o dotazovacím jazyku pro směrování zpráv najdete [v tématu dotazy ve směrování zpráv](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Nedotazované dotazy na zařízení a moduly

[Vlákna zařízení](iot-hub-devguide-device-twins.md) a [vlákna modulu](iot-hub-devguide-module-twins.md) mohou obsahovat libovolné objekty JSON jako značky a vlastnosti. IoT Hub vám umožní dotazovat se na vlákna a vlákna zařízení jako na jeden dokument JSON obsahující všechny zdvojené informace.

Předpokládejme například, že vaše zařízení IoT Hub má následující strukturu (nevlákenný modul by byl podobný jako další moduleId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Nevlákenné dotazy zařízení

IoT Hub zpřístupňuje zařízení jako kolekci dokumentů s názvem **zařízení**. Například následující dotaz načte celou sadu vláken zařízení:

```sql
SELECT * FROM devices
```

> [!NOTE]
> Sady [SDK Azure IoT](iot-hub-devguide-sdks.md) podporují stránkování velkých výsledků.

IoT Hub umožňuje načíst filtrování vláken zařízení s libovolnými podmínkami. Například pro příjem vláken zařízení, kde je značka **Location. region** nastavená na **US** , použijte následující dotaz:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Logické operátory a aritmetické porovnání jsou také podporovány. Pokud například chcete načíst vlákna v zařízení umístěná v USA a nakonfigurovat pro odeslání telemetrie méně než každou minutu, použijte následující dotaz:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Jako pohodlí je také možné použít konstanty pole s operátory **in** a **NZA** (Not in). Chcete-li například načíst vlákna zařízení, která hlásí připojení Wi-Fi nebo kabelové připojení, použijte následující dotaz:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Často je potřeba identifikovat všechna vlákna zařízení, která obsahují určitou vlastnost. IoT Hub podporuje funkci `is_defined()` pro tento účel. Chcete-li například načíst vlákna zařízení, která definují `connectivity` vlastnost, použijte následující dotaz:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Úplný odkaz na možnosti filtrování najdete v části [klauzule WHERE](iot-hub-devguide-query-language.md#where-clause) .

Seskupení a agregace jsou také podporovány. Chcete-li například zjistit počet zařízení v jednotlivých stavech konfigurace telemetrie, použijte následující dotaz:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Tento dotaz seskupení vrátí výsledek podobný následujícímu příkladu:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

V tomto příkladu tři zařízení nahlásila úspěšnou konfiguraci, dvě stále používají konfiguraci a jedna ohlásila chybu.

Projekce dotazům umožňuje vývojářům vracet pouze vlastnosti, o kterých se stará. Pokud například chcete načíst čas poslední aktivity všech odpojených zařízení, použijte následující dotaz:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Moduly s dvojitými dotazy

Dotazování na vlákna modulu je podobné jako dotazování na vlákna zařízení, ale použití jiné kolekce nebo oboru názvů. místo ze **zařízení**se můžete dotazovat ze **zařízení. moduly**:

```sql
SELECT * FROM devices.modules
```

Nepovolujeme spojení mezi zařízeními a zařízeními. kolekce modulů. Pokud se chcete dotazovat na modul na všech zařízeních, udělejte to na základě značek. Tento dotaz vrátí všechny nevlákenné moduly napříč všemi zařízeními se stavem prohledávání:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Tento dotaz vrátí všechny nevlákenné moduly se stavem prohledávání, ale pouze v zadané podmnožině zařízení:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Příklad jazyka C#

Funkce dotazu se zveřejňuje v [sadě SDK služby C#](iot-hub-devguide-sdks.md) ve třídě **RegistryManager** .

Tady je příklad jednoduchého dotazu:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Instance objektu **Query** má vytvořenou velikost stránky (až 100). Pak se načtou vícenásobné stránky voláním metod **GetNextAsTwinAsync** několikrát.

Objekt dotazu zpřístupňuje více **dalších** hodnot v závislosti na možnosti deserializace vyžadované dotazem. Například při použití projekce jsou objekty typu vlákna nebo úlohy nebo prostý formát JSON.

### <a name="nodejs-example"></a>Příklad Node.js

Funkce dotazu se zveřejňuje v [sadě Azure IoT Service SDK pro Node.js](iot-hub-devguide-sdks.md) v objektu **registru** .

Tady je příklad jednoduchého dotazu:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Instance objektu **Query** má vytvořenou velikost stránky (až 100). Pak se načtou vícenásobné stránky voláním metody **nextAsTwin** několikrát.

Objekt dotazu zpřístupňuje více **dalších** hodnot v závislosti na možnosti deserializace vyžadované dotazem. Například při použití projekce jsou objekty typu vlákna nebo úlohy nebo prostý formát JSON.

### <a name="limitations"></a>Omezení

> [!IMPORTANT]
> Výsledkem dotazu může být několik minut zpoždění s ohledem na nejnovější hodnoty v nevlákenách zařízení. Pokud se dotazuje jednotlivé zařízení na základě ID, použijte [REST API získat dvojitou](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.devicetwin?view=azure-java-stable). Toto rozhraní API vždycky vrátí nejnovější hodnoty a má vyšší omezení omezování. REST API můžete vystavit přímo nebo použít ekvivalentní funkce v jedné ze [sad SDK služby Azure IoT Hub](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

V současné době jsou porovnání podporovány pouze mezi primitivními typy (žádné objekty), například `... WHERE properties.desired.config = properties.reported.config` je podporována pouze v případě, že tyto vlastnosti mají primitivní hodnoty.

## <a name="get-started-with-jobs-queries"></a>Začínáme s dotazy na úlohy

[Úlohy](iot-hub-devguide-jobs.md) poskytují způsob, jak provádět operace se sadami zařízení. Všechna vlákna zařízení obsahují informace o úlohách, které jsou součástí kolekce s názvem **úlohy**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

V současné době je tato kolekce Queryable jako **Devices.Jobs** v dotazovacím jazyku IoT Hub.

> [!IMPORTANT]
> V současné době se při dotazování na vlákna zařízení nikdy nevrátí vlastnost Jobs. To znamená, že dotazy obsahující "ze zařízení". K vlastnosti Jobs lze získat přímý pøístup pouze pomocí dotazů `FROM devices.jobs` .
>
>

Pokud například chcete získat všechny úlohy (v minulosti i naplánované), které mají vliv na jedno zařízení, můžete použít následující dotaz:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Všimněte si, jak tento dotaz poskytuje pro každou vrácenou úlohu stav specifický pro zařízení (a případně přímo odpověď metody).

Je také možné filtrovat s libovolnými logickými podmínkami pro všechny vlastnosti objektů v kolekci **Devices.Jobs** .

Chcete-li například načíst všechny dokončené úlohy aktualizace, které byly vytvořeny po září 2016 pro konkrétní zařízení, použijte následující dotaz:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Můžete také načíst výsledky jedné úlohy podle zařízení.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Omezení

V současné době dotazy na **Devices.Jobs** nepodporují:

* Výčnělky `SELECT *` jsou proto možné pouze.
* Podmínky, které kromě vlastností úlohy odkazují na vlákna zařízení (viz předchozí část).
* Provádění agregací, například Count, AVG, seskupit podle.

## <a name="basics-of-an-iot-hub-query"></a>Základy dotazu IoT Hub

Každý IoT Hub dotaz se skládá z klauzulí SELECT a FROM s volitelnými klauzulemi WHERE a GROUP BY. Každý dotaz se spouští na kolekci dokumentů JSON, například na vlákna zařízení. Klauzule FROM označuje kolekci dokumentů, na které se má iterovat (**zařízení**, **zařízení. moduly**nebo **Devices.Jobs**). Pak je použit filtr v klauzuli WHERE. U agregací jsou výsledky tohoto kroku seskupené podle zadání v klauzuli GROUP BY. Pro každou skupinu se vygeneruje řádek, jak je uvedeno v klauzuli SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Klauzule FROM

Klauzule **from <from_specification>** může předpokládat jenom tři hodnoty: **ze zařízení** , aby se dotazoval na vlákna zařízení, **ze zařízení. moduly** na vlákna dotazů v modulu, nebo **z Devices.Jobs** k dotazování na podrobnosti o jednotlivých zařízeních.

## <a name="where-clause"></a>Klauzule WHERE

Klauzule **where <filter_condition>** je volitelná. Určuje jednu nebo více podmínek, které musí dokumenty JSON v kolekci FROM splňovat, aby byly součástí výsledku. Každý dokument JSON musí vyhodnotit zadané podmínky na hodnotu "pravda", které mají být zahrnuty do výsledku.

Povolené podmínky jsou popsány v části [výrazy a podmínky](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Klauzule SELECT

**>příkazu SELECT <select_list** je povinný a určuje, které hodnoty se z dotazu načítají. Určuje hodnoty JSON, které se mají použít ke generování nových objektů JSON.
Pro každý prvek filtrované (a volitelně seskupené) podmnožiny kolekce FROM vytvoří fáze projekce nový objekt JSON. Tento objekt je vytvořen s hodnotami zadanými v klauzuli SELECT.

Následuje gramatika klauzule SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** odkazuje na jakoukoliv vlastnost dokumentu JSON v kolekci from. Některé příklady klauzulí SELECT najdete v části Začínáme s dvojitými dotazy na zařízení.

V současné době jsou klauzule výběru jiné než **Select*** podporovány pouze v agregačních dotazech na vlákna zařízení.

## <a name="group-by-clause"></a>Klauzule GROUP BY

Klauzule **Group by <group_specification>** je volitelný krok, který se spustí po filtru zadaném v klauzuli WHERE, a před projekcí určenou v příkazu SELECT. Seskupuje dokumenty na základě hodnoty atributu. Tyto skupiny se používají ke generování agregovaných hodnot, které jsou zadány v klauzuli SELECT.

Příkladem dotazu pomocí GROUP BY je:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Oficiální syntaxe pro GROUP BY je:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** odkazuje na jakoukoliv vlastnost dokumentu JSON v kolekci from.

V současné době je klauzule GROUP BY podporována pouze při dotazování na vlákna zařízení.

> [!IMPORTANT]
> Termín `group` je v současné době považován za speciální klíčové slovo v dotazech. V případě, že použijete `group` jako název vlastnosti, zvažte její použití v dvojité závorce, aby se předešlo chybám, např. `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'` .
>

## <a name="expressions-and-conditions"></a>Výrazy a podmínky

Na vysoké úrovni *výraz*:

* Vyhodnotí na instanci typu JSON (například Boolean, Number, String, Array nebo Object).
* Je definován pomocí manipulace s daty z dokumentu a konstantami v zařízení JSON pomocí integrovaných operátorů a funkcí.

*Podmínky* jsou výrazy, které se vyhodnotí jako logická hodnota. Jakékoli konstanty, které jsou jiné než logická **hodnota true** , se považují za **false**. Toto pravidlo zahrnuje **hodnoty null**, **undefined**, jakýkoli objekt nebo pole instance, jakýkoli řetězec a logickou **hodnotu false**.

Syntaxe výrazů je:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Chcete-li zjistit, jakým způsobem má každý symbol ve syntaxi výrazů, použijte následující tabulku:

| Symbol | Definice |
| --- | --- |
| attribute_name | Jakákoli vlastnost dokumentu JSON v kolekci **from** . |
| binary_operator | Jakýkoli binární operátor uvedený v části [Operators](#operators) . |
| function_name| Libovolná funkce uvedená v části [Functions](#functions) . |
| decimal_literal |Float vyjádřený v desítkovém zápisu. |
| hexadecimal_literal |Číslo vyjádřené řetězcem 0x následovaným řetězcem hexadecimálních číslic. |
| string_literal |Řetězcové literály jsou řetězce Unicode reprezentované sekvencí nula nebo více znaků Unicode nebo řídicí sekvence. Řetězcové literály jsou uzavřeny v jednoduchých uvozovkách nebo dvojitých uvozovkách. Povolené řídicí znaky: `\'` , `\"` , `\\` , `\uXXXX` pro znaky Unicode definované 4 šestnáctkovými číslicemi. |

### <a name="operators"></a>Operátory

Podporovány jsou následující operátory:

| Rodina | Operátory |
| --- | --- |
| Aritmetické |+, -, *, /, % |
| Logické |A, NEBO, NOT |
| Porovnání |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

Při dotazování na vlákna a úlohy je jedinou podporovanou funkcí:

| Funkce | Popis |
| -------- | ----------- |
| IS_DEFINED (vlastnost) | Vrátí logickou hodnotu, která znamená, zda byla vlastnost přiřazena hodnota (včetně `null` ). |

V podmínkách směrování jsou podporovány následující matematické funkce:

| Funkce | Popis |
| -------- | ----------- |
| ABS (x) | Vrátí absolutní (kladnou) hodnotu zadaného číselného výrazu. |
| EXP (x) | Vrátí exponenciální hodnotu zadaného číselného výrazu (e ^ x). |
| NAPÁJENÍ (x, y) | Vrátí hodnotu zadaného výrazu na zadané napájení (x ^ y).|
| ČTVERCOVÝ (x)    | Vrátí druhou mocninu zadané číselné hodnoty. |
| STROP (x) | Vrátí nejmenší celočíselnou hodnotu, která je větší než nebo rovna zadanému numerickému výrazu. |
| Podlahová plocha (x) | Vrací největší celé číslo menší nebo rovno zadanému numerickému výrazu. |
| PODEPSAT (x) | Vrátí kladné znaménko (+ 1), nula (0) nebo záporné (-1) znaménko zadaného číselného výrazu.|
| SQRT (x) | Vrátí druhou odmocninu zadané číselné hodnoty. |

V podmínkách směrování jsou podporovány následující funkce kontroly a přetypování typů:

| Funkce | Popis |
| -------- | ----------- |
| AS_NUMBER | Převede vstupní řetězec na číslo. `noop` Pokud je vstup číslo, `Undefined` Pokud řetězec nepředstavuje číslo.|
| IS_ARRAY | Vrací logickou hodnotu označující, zda je typ zadaného výrazu pole Array. |
| IS_BOOL | Vrací logickou hodnotu označující, zda je typ zadaného výrazu logická hodnota. |
| IS_DEFINED | Vrátí logickou hodnotu, která znamená, zda byla vlastnost přiřazena hodnota. To je podporováno pouze v případě, že je hodnota primitivního typu. Primitivní typy zahrnují řetězec, Boolean, Numeric nebo `null` . Hodnoty DateTime, typy objektů a pole nejsou podporovány. |
| IS_NULL | Vrací logickou hodnotu označující, zda je typ zadaného výrazu null. |
| IS_NUMBER | Vrací logickou hodnotu označující, zda je typ zadaného výrazu číslo. |
| IS_OBJECT | Vrací logickou hodnotu označující, zda je typ zadaného výrazu objekt JSON. |
| IS_PRIMITIVE | Vrací logickou hodnotu označující, zda je typ zadaného výrazu primitivní (řetězec, logická hodnota, číselná hodnota nebo `null` ). |
| IS_STRING | Vrací logickou hodnotu označující, zda je typ zadaného výrazu řetězec. |

V podmínkách směrování jsou podporovány následující řetězcové funkce:

| Funkce | Popis |
| -------- | ----------- |
| CONCAT (x, y,...) | Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot. |
| Délka (x) | Vrátí počet znaků zadaného řetězcového výrazu.|
| DOLNÍ (x) | Vrátí řetězcový výraz po převedení velkých znakových dat na malá písmena. |
| HORNÍ (x) | Vrátí řetězcový výraz po převodu malých znakových dat na velká písmena. |
| Substring (řetězec; Start [; length]) | Vrátí část řetězcového výrazu počínaje zadaným znakem na základě nuly a pokračuje určenou délkou nebo na konci řetězce. |
| INDEX_OF (řetězec, fragment) | Vrátí počáteční pozici prvního výskytu druhého řetězcového výrazu v rámci prvního zadaného řetězcového výrazu nebo-1, pokud řetězec nebyl nalezen.|
| STARTS_WITH (x, y) | Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz začíná druhým. |
| ENDS_WITH (x, y) | Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz končí druhým. |
| OBSAHUJE (x, y) | Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz obsahuje sekundu. |

## <a name="next-steps"></a>Další kroky

Naučte se spouštět dotazy v aplikacích pomocí [sad Azure IoT SDK](iot-hub-devguide-sdks.md).
