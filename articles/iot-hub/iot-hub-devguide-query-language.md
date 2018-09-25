---
title: Vysvětlení dotazovací jazyk služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – popis služby IoT Hub podobném SQL dotazovací jazyk používá k načtení informací o zařízení a modul dvojčata a úlohy ze služby IoT hub.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 2e4b356fec642e06e3223700967eeacd19f1c49c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952473"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Dotazovací jazyk služby IoT Hub pro dvojčata zařízení a modul, úlohy a směrování zpráv

Centrum IoT poskytuje výkonné jazyce podobném SQL k načtení informací o [dvojčata zařízení] [ lnk-twins] a [úlohy][lnk-jobs]a [směrování zpráv][lnk-devguide-messaging-routes]. Tento článek představuje:

* Úvod do hlavní funkce dotazovací jazyk služby IoT Hub, a
* Podrobný popis jazyka. Podrobnosti o dotazovací jazyk pro směrování zpráv, najdete v části [dotazy v směrování zpráv](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Dotazů na dvojčata zařízení a modul
[Dvojčata zařízení] [ lnk-twins] a dvojčaty modulů může obsahovat libovolné objekty JSON vlastnosti a značky. IoT Hub umožňuje dvojčaty modulů a dvojčata zařízení dotazu jako jeden dokument JSON obsahující informace o veškerém dvojčete.
Předpokládejme například, že vaše dvojčata zařízení centra IoT mají následující strukturu (dvojče zařízení by se měl podobat pouze s další moduleId):

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

### <a name="device-twin-queries"></a>Dotazy dvojčete zařízení

Služba IoT Hub zpřístupní dvojčata zařízení jako kolekce dokumentů s názvem **zařízení**.
Proto následující dotaz načte celou sadu dvojčata zařízení:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Sady SDK Azure IoT] [ lnk-hub-sdks] podporuje stránkování výsledků velký.

IoT Hub umožňuje načíst dvojčata zařízení filtrování pomocí libovolné podmínky. Například pro příjem zařízení dvojčat where **location.region** značka je nastavená na **USA** následující dotaz:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Logické operátory a aritmetické porovnání jsou také podporovány. Dvojčata nachází ve Spojených státech a nakonfigurovaná k odesílání telemetrických dat kratší než každou minutu použijte k načtení zařízení následující dotaz:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

V zájmu usnadnění práce, je také možné použít konstanty pole s **v** a **NVE** operátory (ne v). Například k načtení dvojčata zařízení, které podléhají Wi-Fi nebo drátová připojení použijte tento dotaz:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Často je potřeba identifikovat všechny dvojčata zařízení, které obsahují určitou vlastnost. IoT Hub podporuje funkci `is_defined()` pro tento účel. Například k načtení dvojčata zařízení, které definují `connectivity` vlastnosti použijte tento dotaz:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Odkazovat [klauzule WHERE] [ lnk-query-where] najdete úplný přehled možností filtrování.

Seskupení a agregace jsou také podporovány. Najít počet zařízení v jednotlivých telemetrii, stav konfigurace použijte tento dotaz:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Toto seskupení dotaz by vrátil výsledek podobný následujícím příkladu:

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

V tomto příkladu tři zařízení hlásí úspěšné konfigurace, dva jsou stále použití konfigurace a jeden ohlásil chybu.

Projekce dotazů umožňují vývojářům vrátit pouze vlastnosti, které se o ně stojí. Například k načtení poslední čas aktivity všech odpojení zařízení použijte následující dotaz:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Dotazy dvojčete modulu

Při dotazování u dvojčaty modulů je podobný dotaz na dvojčata zařízení, ale pomocí jiné kolekce/oboru názvů, tedy ne "zařízení z" můžete zadat dotaz

```sql
SELECT * FROM devices.modules
```

Nepovolit jsme spojení mezi zařízeními a devices.modules kolekce. Pokud chcete dotaz dvojčaty modulů napříč zařízeními, můžete to udělat na základě značek. Tento dotaz vrátí všechny dvojčaty modulů napříč všemi zařízeními se stavem prohledávání:

```sql
Select * from devices.modules where properties.reported.status = 'scanning'
```

Tento dotaz vrátí všechny dvojčaty modulů s kontroly stavu, ale jenom na zadaný dílčí sadu zařízení.

```sql
Select * from devices.modules where properties.reported.status = 'scanning' and deviceId IN ('device1', 'device2')  
```

### <a name="c-example"></a>Příklad jazyka C#
Funkce dotazu je zveřejněna rozhraním [sady SDK služby jazyka C#] [ lnk-hub-sdks] v **RegistryManager** třídy.
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

**Dotazu** s velikostí stránky (až 100) je vytvořena instance objektu. Pak více stránek se načítají pomocí volání **GetNextAsTwinAsync** metody více než jednou.

Objekt dotazu vystavuje více **Další** hodnoty v závislosti na možnosti deserializace vyžadované dotazu. Například objekty úlohy nebo dvojčat zařízení nebo prostý JSON při použití projekce.

### <a name="nodejs-example"></a>Příklad Node.js
Funkce dotazu je zveřejněna rozhraním [pro službu Azure IoT SDK pro Node.js] [ lnk-hub-sdks] v **registru** objektu.
Tady je příklad jednoduchého dotazu:

```nodejs
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

**Dotazu** s velikostí stránky (až 100) je vytvořena instance objektu. Pak více stránek se načítají pomocí volání **nextAsTwin** metodu více než jednou.

Objekt dotazu vystavuje více **Další** hodnoty v závislosti na možnosti deserializace vyžadované dotazu. Například objekty úlohy nebo dvojčat zařízení nebo prostý JSON při použití projekce.

### <a name="limitations"></a>Omezení

> [!IMPORTANT]
> Výsledky dotazu může mít několik minut, než zpoždění s ohledem na nejnovější hodnoty v dvojčata zařízení. Pokud se dotaz na dvojčata zařízení jednotlivých podle ID, pomocí rozhraní API dvojčete zařízení načtení. Toto rozhraní API vždy obsahuje nejnovější hodnoty a má vyšší omezení.

V současné době porovnání jsou podporovány pouze mezi primitivní typy (žádné objekty), například `... WHERE properties.desired.config = properties.reported.config` je podporována pouze v případě, že máte primitivní hodnoty těchto vlastností.

## <a name="get-started-with-jobs-queries"></a>Začínáme s dotazy úlohy

[Úlohy] [ lnk-jobs] poskytují způsob, jak provádět operace na sadu zařízení. Dvojče každého zařízení obsahuje informace o úlohy, které je součástí v kolekci s názvem **úlohy**.
Logicky,

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

Tato kolekce je v současné době dotazovatelné příkazem **devices.jobs** ve službě IoT Hub dotazovací jazyk.

> [!IMPORTANT]
> V současné době se nikdy vrátí vlastnost úlohy při dotazování dvojčat zařízení. To znamená, dotazy, které obsahují "ze zařízení". Vlastnosti úlohy je přístupný pouze přímo s dotazy, které používají `FROM devices.jobs`.
>
>

Například k získání všech úloh (posledních a plánované), které mají vliv jedno zařízení, můžete použít následující dotaz:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Všimněte si, jak tento dotaz obsahuje stav specifický pro zařízení (a případně odpovědi přímé metody) jednotlivých úloh vrátila.
Je také možné filtrovat pomocí libovolného logické podmínky u všech vlastností objektu v **devices.jobs** kolekce.
Například pokud chcete načíst všechny úlohy aktualizace dvojčete dokončené zařízení, vytvořené pro konkrétní zařízení od září 2016, použijte tento dotaz:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Můžete také načíst výsledky na zařízení z jedné úlohy.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Omezení
V současné době se dotazuje na **devices.jobs** nepodporují:

* Projekce, proto pouze `SELECT *` je možné.
* Podmínky, které odkazují na dvojče zařízení kromě vlastnosti úlohy (viz předchozí část).
* Provádění agregací, jako je například počet, průměr, Seskupit podle.

## <a name="basics-of-an-iot-hub-query"></a>Základní informace o službě IoT Hub dotazu
Každý dotaz služby IoT Hub se skládá, vyberte a z klauzule volitelné místo, kde a klauzule GROUP BY. Každý dotaz se spouští v kolekci dokumentů JSON, například dvojčata zařízení. Klauzule FROM označuje provést iteraci v kolekci dokumentů (**zařízení** nebo **devices.jobs**). Poté je použit filtr v klauzuli WHERE. Pomocí agregace, jsou výsledky tohoto kroku seskupené podle zadání v klauzuli Group by. Pro každou skupinu, je vygenerována řádku zadané v klauzuli SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM – klauzule
**z < from_specification >** klauzule může převzít jenom dvě hodnoty: **ze zařízení** na dvojčata zařízení dotazu, nebo **z devices.jobs** na podrobnosti o dotazu úlohy na zařízení.

## <a name="where-clause"></a>Klauzule WHERE
**Kde < filter_condition >** klauzule je volitelný. Určuje, že mají být zahrnuty jako součást výsledku musí splňovat jednu nebo více podmínek, že v kolekci z dokumentů JSON. Jakýkoliv dokument JSON musí být zadané podmínky na "true" mají být zahrnuty ve výsledku.

Povolené podmínky jsou popsány v části [výrazy a podmínky][lnk-query-expressions].

## <a name="select-clause"></a>Klauzule SELECT
**Vyberte < select_list >** je povinná a určuje, jaké hodnoty jsou načteny z dotazu. Určuje hodnoty JSON se použije k vygenerování nových objektů JSON.
Pro každý prvek filtrované (a volitelně seskupené) podmnožinu kolekce z fáze projekce vygeneruje nový objekt JSON. Tento objekt je vytvořený pomocí hodnoty zadané v klauzuli SELECT.

Toto je gramatika klauzuli SELECT:

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

**Attribute_name v relaci** odkazuje na všechny vlastnosti v kolekci z dokumentu JSON. Některé příklady klauzule FROM najdete v [Začínáme s dotazy dvojčete zařízení] [ lnk-query-getstarted] oddílu.

V současné době výběr klauzule liší od **vyberte*** jsou podporovány pouze v agregačních dotazů na dvojčata zařízení.

## <a name="group-by-clause"></a>Klauzule GROUP BY
**Group < group_specification >** klauzule je volitelný krok, který se spustí po zadaný v klauzuli WHERE a před projekce určená v seznamu vyberte filtr. Seskupuje dokumentů na základě hodnoty atributu. Tyto skupiny slouží ke generování agregovaných hodnot zadaných v klauzuli SELECT.

Příklad dotazu pomocí GROUP BY je:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Formální syntaxe pro GROUP BY je následující:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name v relaci** odkazuje na všechny vlastnosti v kolekci z dokumentu JSON.

V klauzuli GROUP BY se v současné době podporuje jenom při dotazování dvojčat zařízení.

## <a name="expressions-and-conditions"></a>Výrazy a podmínky
Na vysoké úrovni *výraz*:

* Vyhodnotí jako instanci typu JSON (jako je logická hodnota, číslo, řetězec, pole nebo objekt).
* Je definovaná pomocí manipulace s daty z dokumentu JSON zařízení a konstant pomocí integrované operátory a funkce.

*Podmínky* jsou výrazy, které vedou na logickou hodnotu. Libovolná konstanta liší od datový typ Boolean **true** považuje **false**. Toto pravidlo zahrnuje **null**, **nedefinované**, libovolný objekt nebo pole instance, libovolný řetězec a logickou hodnotu **false**.

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

Informace o tom co zastupuje každý symbol v syntaxi výrazů, najdete v následující tabulce:

| Symbol | Definice |
| --- | --- |
| attribute_name v relaci | Nějaká vlastnost v dokumentu JSON **FROM** kolekce. |
| binary_operator | Žádné binární operátor uvedené v [operátory](#operators) oddílu. |
| Název_funkce| Všechny funkce uvedené v [funkce](#functions) oddílu. |
| decimal_literal |Plovoucí desetinné čárky v desítkovém zápisu. |
| hexadecimal_literal |Číslo vyjadřuje řetězcem "0 x", za nímž následuje řetězec šestnáctkových číslic. |
| řetězcový_literál |Řetězcové literály jsou reprezentovány posloupnost nula nebo více znaků Unicode nebo řídicí sekvence řetězců v kódu Unicode. Řetězcové literály jsou uzavřeny v jednoduchých uvozovkách nebo dvojité uvozovky. Povolené řídicí sekvence: `\'`, `\"`, `\\`, `\uXXXX` znaků Unicode, které jsou definovány 4 šestnáctkovými číslicemi. |

### <a name="operators"></a>Operátory
Jsou podporovány následující operátory:

| Rodina | Operátory |
| --- | --- |
| Aritmetické operace |+, -, *, /, % |
| Logické |A, NEBO NE |
| porovnání |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
Při dotazování na dvojčata a úlohy, které jediný podporovaný je funkce:

| Funkce | Popis |
| -------- | ----------- |
| IS_DEFINED(Property) | Vrátí logickou hodnotu označující, pokud vlastnost byla přiřazena hodnota (včetně `null`). |

V podmínkách trasy jsou podporovány následující matematické funkce:

| Funkce | Popis |
| -------- | ----------- |
| ABS(x) | Vrátí absolutní hodnotu (pozitivní) zadaný číselný výraz. |
| EXP(x) | Vrátí hodnotu exponenciální zadaný číselný výraz (e ^ x). |
| Power(x,y) | Vrátí hodnotu zadaného výrazu určenou (x ^ y).|
| Square(x) | Vrátí druhou mocninu zadané číselnou hodnotu. |
| CEILING(x) | Vrátí nejmenší hodnotu celé číslo větší než nebo rovna zadané číselný výraz. |
| Floor(x) | Vrátí největší celé číslo menší nebo rovna zadané číselný výraz. |
| Sign(x) | Vrátí kladné (+ 1), nula (0) nebo záporné znaménko (-1), z určeného číselného výrazu.|
| SQRT(x) | Vrátí druhou odmocninu zadanou číselnou hodnotu. |

V podmínkách trasy jsou podporovány následující kontroly typu a funkce přetypování:

| Funkce | Popis |
| -------- | ----------- |
| AS_NUMBER | Převede vstupní řetězec na číslo. `noop` Pokud je vstup na číslo. `Undefined` Pokud řetězec nepředstavuje číslo.|
| IS_ARRAY – | Vrátí logickou hodnotu označující, zda je typ z určeného výrazu pole. |
| IS_BOOL | Vrátí logickou hodnotu označující, pokud typ z určeného výrazu je logická hodnota. |
| IS_DEFINED | Vrátí logickou hodnotu označující, pokud vlastnost byla přiřazena hodnota. |
| IS_NULL | Vrátí logickou hodnotu označující, zda je typ z určeného výrazu hodnotu null. |
| IS_NUMBER | Vrátí logickou hodnotu označující, pokud typ z určeného výrazu je číslo. |
| IS_OBJECT – | Vrátí logickou hodnotu označující, pokud je typ z určeného výrazu objektu JSON. |
| IS_PRIMITIVE | Vrátí logickou hodnotu označující, zda je typ z určeného výrazu jednoduchého typu (string, Boolean, číselná, nebo `null`). |
| IS_STRING | Vrátí logickou hodnotu označující, pokud je typ z určeného výrazu string. |

V podmínkách trasy jsou podporovány následující funkce řetězec:

| Funkce | Popis |
| -------- | ----------- |
| CONCAT (x, y,...) | Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot. |
| LENGTH(x) | Vrátí počet znaků ze zadaného řetězcového výrazu.|
| LOWER(x) | Vrátí řetězcový výraz po převedení dat velkým písmenem na malá písmena. |
| UPPER(x) | Vrátí řetězcový výraz po převedení data znaků na malá písmena na velká písmena. |
| Dílčí řetězec (string, start [, délka]) | Vrátí část řetězcového výrazu počínaje pozice s nulovým základem zadaný znak a pokračuje na určenou délku nebo na konci řetězce. |
| INDEX_OF (string, fragment) | Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec.|
| STARTS_WITH (x, y) | Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce začíná druhé. |
| ENDS_WITH (x, y) | Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce končí na druhý. |
| CONTAINS(x,y) | Vrátí hodnotu typu Boolean označující, zda řetězec prvního výrazu obsahuje druhý. |

## <a name="next-steps"></a>Další postup
Zjistěte, jak provádět dotazy ve svých aplikacích pomocí [sad SDK Azure IoT][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
