---
title: Principy dotazovacího jazyka Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – popis dotazovacího jazyka IoT Hub podobnésql, který se používá k načtení informací o dvojčatech zařízení nebo modulu a úlohách z vašeho centra IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.openlocfilehash: bcc53322ac6942b52853be561bc3441e23fbf53b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632935"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Dotazovací jazyk služby IoT Hub pro dvojčata zařízení a modulů, úlohy a směrování zpráv

IoT Hub poskytuje výkonný jazyk podobný SQL pro načtení informací o [dvojčatech zařízení](iot-hub-devguide-device-twins.md), [dvojčata modulů](iot-hub-devguide-module-twins.md), [úlohy](iot-hub-devguide-jobs.md)a [směrování zpráv](iot-hub-devguide-messages-d2c.md). Tento článek představuje:

* Úvod k hlavním funkcím dotazovacího jazyka IoT Hub a
* Podrobný popis jazyka. Podrobnosti o jazyce dotazů pro směrování zpráv naleznete [v tématu dotazy ve směrování zpráv](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Dotazy na dvojče zařízení a modul

[Dvojčata zařízení](iot-hub-devguide-device-twins.md) a [dvojčata modulu](iot-hub-devguide-module-twins.md) mohou obsahovat libovolné objekty JSON jako značky i vlastnosti. IoT Hub umožňuje dotaz ovat dvojčata zařízení a dvojčata modulů jako jeden dokument JSON obsahující všechny informace o dvojčatech.

Předpokládejme například, že dvojčata zařízení služby IoT hub mají následující strukturu (dvojče modulu by bylo podobné pouze s další modulId):

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

### <a name="device-twin-queries"></a>Dotazy na dvojče zařízení

IoT Hub zveřejňuje dvojčata zařízení jako kolekci dokumentů s názvem **zařízení**. Například následující dotaz načte celou sadu dvojčat zařízení:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Sady Azure IoT SDK](iot-hub-devguide-sdks.md) podporují stránkování velkých výsledků.

IoT Hub umožňuje načíst filtrování dvojčat zařízení s libovolnými podmínkami. Chcete-li například přijímat dvojčata zařízení, kde je značka **location.region** nastavena na **US,** použijte následující dotaz:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Podporovány jsou také logické operátory a aritmetické porovnání. Chcete-li například načíst dvojčata zařízení umístěná v USA a nakonfigurovaná tak, aby odesílala telemetrii menší než každou minutu, použijte následující dotaz:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Pro pohodlí je také možné použít maticové konstanty s operátory **IN** a **NIN** (není v). Chcete-li například načíst dvojčata zařízení, která hlásí WiFi nebo kabelové připojení, použijte následující dotaz:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Často je nutné identifikovat všechna dvojčata zařízení, které obsahují určitou vlastnost. IoT Hub podporuje `is_defined()` funkci pro tento účel. Například načíst dvojčata zařízení, které definují `connectivity` vlastnost použijte následující dotaz:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Úplný odkaz na možnosti filtrování naleznete v části [klauzule WHERE.](iot-hub-devguide-query-language.md#where-clause)

Seskupení a agregace jsou také podporovány. Chcete-li například najít počet zařízení v každém stavu konfigurace telemetrie, použijte následující dotaz:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Tento dotaz seskupení by vrátit výsledek podobný následující příklad:

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

V tomto příkladu tři zařízení hlášeny úspěšné konfigurace, dvě stále používají konfiguraci a jeden ohlásil chybu.

Projekční dotazy umožňují vývojářům vrátit pouze vlastnosti, které jim záleží. Chcete-li například načíst čas poslední aktivity všech odpojených zařízení, použijte následující dotaz:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Dotazy na dvojče modulu

Dotazování na dvojčata modulu je podobné dotazování na dvojčata zařízení, ale pomocí jinékolekce/oboru názvů; místo ze **zařízení**, dotaz z **devices.modules**:

```sql
SELECT * FROM devices.modules
```

Nepovolujeme spojení mezi zařízeními a kolekcemi devices.modules. Pokud chcete dotazovat dvojčata modulu napříč zařízeními, uděláte to na základě značek. Tento dotaz vrátí všechna dvojčata modulu ve všech zařízeních se stavem skenování:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Tento dotaz vrátí všechna dvojčata modulu se stavem skenování, ale pouze na určené podmnožině zařízení:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Příklad jazyka C#

Funkce dotazu je vystavena [sadou C# service SDK](iot-hub-devguide-sdks.md) ve třídě **RegistryManager.**

Zde je příklad jednoduchého dotazu:

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

Objekt **dotazu** je vytvořena instance s velikostí stránky (až 100). Potom více stránek jsou načteny voláním **GetNextAsTwinAsync** metody vícekrát.

Objekt dotazu zveřejňuje více **Další** hodnoty, v závislosti na možnosti deserializace vyžadované dotazem. Například dvojče zařízení nebo objekty úlohy nebo prostý JSON při použití projekce.

### <a name="nodejs-example"></a>Příklad souboru Node.js

Funkce dotazu je vystavena [službou Azure IoT SDK pro Node.js](iot-hub-devguide-sdks.md) v objektu **Registru.**

Zde je příklad jednoduchého dotazu:

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

Objekt **dotazu** je vytvořena instance s velikostí stránky (až 100). Pak více stránek jsou načteny voláním **nextAsTwin** metoda vícekrát.

Objekt dotazu zveřejňuje více **Další** hodnoty, v závislosti na možnosti deserializace vyžadované dotazem. Například dvojče zařízení nebo objekty úlohy nebo prostý JSON při použití projekce.

### <a name="limitations"></a>Omezení

> [!IMPORTANT]
> Výsledky dotazu může mít několik minut zpoždění s ohledem na nejnovější hodnoty v dvojčata zařízení. Pokud dotaznat jednotlivých dvojčat zařízení podle ID, použijte [získat dvojče ROZHRANÍ REST API](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin). Toto rozhraní API vždy vrátí nejnovější hodnoty a má vyšší omezení omezení. Rozhraní REST API můžete vydat přímo nebo použít ekvivalentní funkce v jedné z [sad Azure IoT Hub Service SDK](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

V současné době jsou porovnání podporována pouze mezi `... WHERE properties.desired.config = properties.reported.config` primitivními typy (žádné objekty), například je podporována pouze v případě, že tyto vlastnosti mají primitivní hodnoty.

## <a name="get-started-with-jobs-queries"></a>Začínáme s dotazy na úlohy

[Úlohy](iot-hub-devguide-jobs.md) poskytují způsob, jak provádět operace na sadách zařízení. Každé dvojče zařízení obsahuje informace o úlohách, které je součástí kolekce s názvem **úlohy**.

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

V současné době tato kolekce je dotazovatjako **devices.jobs** v dotazovacím jazyce služby IoT Hub.

> [!IMPORTANT]
> V současné době je vlastnost úlohy nikdy vrácena při dotazování dvojčatzařízení. To znamená dotazy, které obsahují "FROM zařízení". Vlastnost jobs lze přistupovat pouze přímo `FROM devices.jobs`s dotazy pomocí .
>
>

Chcete-li například získat všechny úlohy (minulé a plánované), které ovlivňují jedno zařízení, můžete použít následující dotaz:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Všimněte si, jak tento dotaz poskytuje stav specifický pro zařízení (a případně přímou odpověď metody) každé vrácené úlohy.

Je také možné filtrovat s libovolnými logickými podmínkami na všechny vlastnosti objektu v **kolekci devices.jobs.**

Chcete-li například načíst všechny dokončené úlohy aktualizace dvojčete zařízení, které byly vytvořeny po září 2016 pro konkrétní zařízení, použijte následující dotaz:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Můžete také načíst výsledky pro jednotlivé zařízení jedné úlohy.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Omezení

V současné době dotazy na **devices.jobs** nepodporují:

* Projekce, proto `SELECT *` je možné pouze.
* Podmínky, které odkazují na dvojče zařízení kromě vlastností úlohy (viz předchozí část).
* Provádění agregací, jako je například počet, avg, seskupit podle.

## <a name="basics-of-an-iot-hub-query"></a>Základy dotazu centra IoT Hub

Každý dotaz služby IoT Hub se skládá z klauzulí SELECT a FROM s volitelnými klauzulemi WHERE a GROUP BY. Každý dotaz je spuštěn na kolekci dokumentů JSON, například dvojčata zařízení. Klauzule FROM označuje kolekci dokumentů, která má být iterována na **(zařízení**, **devices.modules**nebo **devices.jobs**). Potom je použit filtr v klauzuli WHERE. S agregacemi jsou výsledky tohoto kroku seskupeny podle klauzule GROUP BY. Pro každou skupinu je generován řádek, jak je uvedeno v klauzuli SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Klauzule FROM

**From <from_specification>** klauzule může převzít pouze tři hodnoty: FROM **zařízení** na dotaz dvojčata zařízení, **FROM devices.modules** na dvojčata modulu dotazu nebo FROM **devices.jobs** dotazu na podrobnosti úlohy na zařízení.

## <a name="where-clause"></a>Klauzule WHERE

Klauzule **WHERE <filter_condition>** je nepovinná. Určuje jednu nebo více podmínek, které musí splňovat dokumenty JSON v kolekci FROM, aby byly zahrnuty jako součást výsledku. Každý dokument JSON musí vyhodnotit zadané podmínky na "true", které mají být zahrnuty do výsledku.

Povolené podmínky jsou popsány v části [Výrazy a podmínky](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Klauzule SELECT

**SELECT <select_list>** je povinné a určuje, jaké hodnoty jsou načteny z dotazu. Určuje hodnoty JSON, které mají být použity ke generování nových objektů JSON.
Pro každý prvek filtrované (a volitelně seskupené) podmnožiny from kolekce, fáze projekce generuje nový objekt JSON. Tento objekt je vytvořen s hodnotami zadanými v klauzuli SELECT.

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

**Attribute_name** odkazuje na všechny vlastnosti dokumentu JSON v kolekci FROM. Některé příklady klauzulí SELECT najdete v části Začínáme s dotazy dvojčete zařízení.

V současné době jsou v souhrnných dotazech na dvojčata zařízení podporovány pouze klauzule o výběru, které se liší od **SELECT**.)

## <a name="group-by-clause"></a>Klauzule GROUP BY

**Klauzule GROUP BY <group_specification>** je volitelný krok, který se provede za filtrem zadaným v klauzuli WHERE a před projekcí zadanou v selectu. Seskupuje dokumenty na základě hodnoty atributu. Tyto skupiny se používají ke generování agregovaných hodnot, jak je uvedeno v klauzuli SELECT.

Příkladem dotazu pomocí funkce GROUP BY je:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Formální syntaxe pro GROUP BY je:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** odkazuje na všechny vlastnosti dokumentu JSON v kolekci FROM.

V současné době group by klauzule je podporována pouze při dotazování dvojčatzařízení.

> [!IMPORTANT]
> Termín `group` je aktuálně považován za speciální klíčové slovo v dotazech. V případě, `group` že použijete název vaší vlastnosti, zvažte jeho obklopení dvojitými závorkami, `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`abyste předešli chybám, například .
>

## <a name="expressions-and-conditions"></a>Výrazy a podmínky

Na vysoké úrovni *výraz*:

* Vyhodnotí se na instanci typu JSON (například boolean, číslo, řetězec, pole nebo objekt).
* Je definovánmanipulací s daty pocházejícími ze zařízení JSON dokumentu a konstantpomocí vestavěných operátorů a funkcí.

*Podmínky* jsou výrazy, které vyhodnocují logickou hodnotu. Jakákoli konstanta odlišná od logické **hodnoty true** je považována za **nepravdivou**. Toto pravidlo obsahuje **null**, **undefined**, libovolný objekt nebo pole instance, libovolný řetězec a logické **false**.

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

Chcete-li pochopit, co každý symbol v syntaxi výrazů znamená, naleznete v následující tabulce:

| Symbol | Definice |
| --- | --- |
| Attribute_name | Všechny vlastnosti dokumentu JSON v kolekci **FROM.** |
| binary_operator | Libovolný binární operátor uvedený v části [Operátoři.](#operators) |
| function_name| Všechny funkce uvedené v části [Funkce.](#functions) |
| decimal_literal |Plovák vyjádřený v desítkové notaci. |
| hexadecimal_literal |Číslo vyjádřené řetězcem '0x' následované řetězcem šestnáctkových číslic. |
| string_literal |Řetězcové literály jsou řetězce Unicode reprezentované posloupností nula nebo více znaků Unicode nebo řídicích sekvencí. Řetězcové literály jsou uzavřeny v jednoduchých uvozovkách nebo dvojitých uvozovkách. Povolené `\'`úniky: `\"` `\\`, `\uXXXX` , , pro znaky Unicode definované 4 šestnáctkovými číslicemi. |

### <a name="operators"></a>Operátory

Podporovány jsou následující operátory:

| Rodina | Operátory |
| --- | --- |
| Aritmetické |+, -, *, /, % |
| Logické |A, NEBO, NEBO NE |
| Srovnání |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

Při dotazování dvojčat a úlohje jedinou podporovanou funkcí:

| Funkce | Popis |
| -------- | ----------- |
| IS_DEFINED (vlastnost) | Vrátí logickou hodnotu označující, zda byla `null`vlastnostpřiřazena hodnota (včetně). |

V podmínkách tras jsou podporovány následující matematické funkce:

| Funkce | Popis |
| -------- | ----------- |
| ABS(x) | Vrátí absolutní (kladnou) hodnotu zadaného číselného výrazu. |
| EXP(x) | Vrátí exponenciální hodnotu zadaného číselného výrazu (e^x). |
| POWER (x,y) | Vrátí hodnotu zadaného výrazu na zadaný výkon (x^y).|
| ČTVEREC(x)    | Vrátí druhou mocninu zadané číselné hodnoty. |
| STROP(x) | Vrátí nejmenší hodnotu celého čísla větší nebo rovnou zadanému číselnému výrazu. |
| PODLAHA (x) | Vrátí největší celé číslo menší nebo rovno zadanému číselnému výrazu. |
| SIGN(x) | Vrátí kladný znak (+1), nula (0) nebo záporný znak (-1) zadaného číselného výrazu.|
| SQRT(x) | Vrátí druhou odmocninu zadané číselné hodnoty. |

V podmínkách tras jsou podporovány následující funkce kontroly a přetypování:

| Funkce | Popis |
| -------- | ----------- |
| AS_NUMBER | Převede vstupní řetězec na číslo. `noop`je-li vstup číslem; `Undefined` pokud řetězec nepředstavuje číslo.|
| IS_ARRAY | Vrátí logickou hodnotu označující, zda je typem zadaného výrazu pole. |
| IS_BOOL | Vrátí logickou hodnotu označující, zda je typ zadaného výrazu logický. |
| IS_DEFINED | Vrátí logickou hodnotu označující, pokud byla vlastnosti přiřazena hodnota. To je podporováno pouze v případě, že hodnota je primitivní typ. Primitivní typy zahrnují řetězec, logickou `null`hodnotu, číselnou nebo . DateTime, typy objektů a pole nejsou podporovány. |
| IS_NULL | Vrátí logickou hodnotu označující, zda je typ zadaného výrazu null. |
| IS_NUMBER | Vrátí logickou hodnotu označující, zda je typem zadaného výrazu číslo. |
| IS_OBJECT | Vrátí logickou hodnotu označující, zda je typem zadaného výrazu objekt JSON. |
| IS_PRIMITIVE | Vrátí logickou hodnotu označující, zda je typ zadaného výrazu primitivní `null`(řetězec, logický, číselný nebo ). |
| IS_STRING | Vrátí logickou hodnotu označující, zda je typem zadaného výrazu řetězec. |

V podmínkách tras jsou podporovány následující funkce řetězce:

| Funkce | Popis |
| -------- | ----------- |
| CONCAT(x, y, ...) | Vrátí řetězec, který je výsledkem zřetězení dvou nebo více hodnot řetězce. |
| DÉLKA (x) | Vrátí počet znaků zadaného řetězcového výrazu.|
| DOLNÍ (x) | Vrátí řetězcový výraz po převodu velkých znakových dat na malá písmena. |
| HORNÍ (x) | Vrátí řetězcový výraz po převodu malých znakových dat na velká písmena. |
| SUBSTRING(řetězec, začátek [, délka]) | Vrátí část řetězcového výrazu začínající na zadané pozici znaku s nulovým základem a pokračuje do zadané délky nebo na konec řetězce. |
| INDEX_OF (řetězec, fragment) | Vrátí počáteční pozici prvního výskytu druhého řetězcového výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud řetězec nebyl nalezen.|
| STARTS_WITH(x, y) | Vrátí logickou hodnotu označující, zda první řetězcový výraz začíná druhým. |
| ENDS_WITH(x, y) | Vrátí logickou hodnotu označující, zda první řetězcový výraz končí druhým. |
| OBSAHUJE(x,y) | Vrátí logickou hodnotu označující, zda první řetězec výraz obsahuje druhý. |

## <a name="next-steps"></a>Další kroky

Zjistěte, jak provádět dotazy ve vašich aplikacích pomocí [sad Azure IoT SDK](iot-hub-devguide-sdks.md).
