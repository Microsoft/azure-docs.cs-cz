---
title: Azure Stream Analytics JavaScript uživatelem definovaných agregacích | Microsoft Docs
description: Provedení mechanismy rozšířený dotaz s uživatelem definovaných agregacích JavaScript
keywords: JavaScript, uživatelem definované agregace, uda
services: stream-analytics
author: minhe-msft
manager: santoshb
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/28/2017
ms.author: minhe
ms.openlocfilehash: 9f9a2e33f97fc90265933ee9941f2d02a92cbc4c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics JavaScript uživatelem definovaných agregacích (Preview)

Azure Stream Analytics podporuje uživatelem definované agregace (UDA) napsané v jazyce JavaScript, umožňuje implementovat komplexní stavová obchodní logiku. V rámci UDA máte plnou kontrolu stavu datová struktura, akumulace stav, stav dekumulaci a agregační výsledek výpočtu. Článek zavádí dvě různé JavaScript UDA rozhraní, postup vytvoření UDA a jak používat UDA se systémem Windows operací v dotazu Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Uživatelem definované agregace JavaScript

Uživatelem definovaná agregace je použít na vrcholku specifikaci okno čas k agregaci událostí v okně a vytvořit jednu výslednou hodnotu. Existují dva typy UDA rozhraní, Stream Analytics podporuje v současné době AccumulateOnly a AccumulateDeaccumulate. Oba typy UDA použitím Přeskakujícího okna, posílání okna a klouzavé okna. AccumulateDeaccumulate UDA provede lépe než AccumulateOnly UDA při použití spolu s posílání a klouzavé okna. Vyberte jednu z těchto dvou typů založený na algoritmu, který používáte.

### <a name="accumulateonly-aggregates"></a>Agregace AccumulateOnly

Agregace AccumulateOnly můžete hromadit pouze nové události do stavu, algoritmus neumožňuje deaccumulation hodnot. Vyberte tento typ agregace při deaccumulate událost informace z hodnoty stavu není možné implementovat. Toto je šablona JavaScript pro AccumulatOnly agregace:

````JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

### <a name="accumulatedeaccumulate-aggregates"></a>Agregace AccumulateDeaccumulate

Agregace AccumulateDeaccumulate povolit deaccumulation předchozí Akumulovaná hodnotu ze stavu, například, odebrat dvojici klíč / hodnota ze seznamu hodnot událostí nebo odečíst hodnotu ze stavu Sum agregační. Toto je šablona JavaScript pro AccumulateDeaccumulate agregace:

````JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

## <a name="uda---javascript-function-declaration"></a>UDA - deklarace funkce jazyka JavaScript

Každý UDA JavaScript je definována deklaraci funkce objektu. Tady jsou důležité elementy v definici UDA.

### <a name="function-alias"></a>Alias funkce

Funkce alias, je identifikátor UDA. Při volání v dotazu Stream Analytics, vždy použijte UDA alias společně s "uda". Předpona.

### <a name="function-type"></a>Typ funkce

Pro UDA, musí být typ funkce **Javascript UDA**.

### <a name="output-type"></a>Typ výstupu

Konkrétní typ této úlohy Stream Analytics podporováno, nebo "Žádné" Pokud chcete ke zpracování typu v dotazu.

### <a name="function-name"></a>Název funkce

Název tohoto objektu funkce. Název funkce názvy shodovat UDA alias (Náhled chování, jsme zvažují anonymní funkce podpory při GA).

### <a name="method---init"></a>Metoda - init()

Metoda init() inicializuje stav agregace. Tato metoda je volána, když se spustí okno.

### <a name="method--accumulate"></a>Metoda – accumulate()

Metoda accumulate() vypočítá UDA stav na základě předchozího stavu a aktuální hodnoty události. Tato metoda je volána, když událost zadá časový interval (TUMBLINGWINDOW, HOPPINGWINDOW nebo SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Metoda – deaccumulate()

Metoda deaccumulate() přepočítá stavu na základě předchozího stavu a aktuální hodnoty události. Tato metoda je volána, když událost opustí SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda – deaccumulateState()

Metoda deaccumulateState() přepočítá stavu na základě předchozího stavu a stavu směrování. Tato metoda je volána, když sada ponechejte události a HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda – computeResult()

Metoda computeResult() vrátí agregovaná výsledků na základě aktuálního stavu. Tato metoda je volána na konci časový interval (TUMBLINGWINDOW, HOPPINGWINDOW a SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA podporované typy vstupní a výstupní data
Datové typy JavaScript UDA, najdete v oddílu **Stream Analytics a JavaScript typ – převod** z [integrovat funkce JavaScript UDF](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Přidání JavaScript UDA z portálu Azure

Níže budeme provede procesem vytvoření UDA z portálu. V příkladu, kterou tady používáme počítá čas vážené průměry.

Nyní vytvoříme JavaScript UDA pod existující úlohy ASA podle následujících kroků.

1. Přihlaste se k portálu Azure a vyhledejte existující úlohu služby Stream Analytics.
1. Klikněte na odkaz funkce v části **úlohy TOPOLOGIE**.
1. Klikněte na **přidat** ikonu, čímž přidáte nové funkce.
1. V zobrazení nové funkce, vyberte **JavaScript UDA** jako typ funkce, pak se zobrazí výchozí šablonu UDA zobrazí v editoru.
1. Vyplňte "TWA" jako UDA alias a změňte implementaci funkce takto:

    ````JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ````

1. Po kliknutí na tlačítko "Uložit" vaší UDA objeví v seznamu funkce.

1. Klikněte na nové funkce "TWA", můžete zkontrolovat v definici funkce.

## <a name="calling-javascript-uda-in-asa-query"></a>Volání metody JavaScript UDA v dotazu ASA

Na portálu Azure a spusťte úlohu, upravte dotaz a volání funkce TWA() s předponou pověření "uda.". Příklad:

````SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
````

## <a name="testing-query-with-uda"></a>Testování dotazů s UDA

Vytvořit místní soubor JSON s níže obsah, nahrát soubor do úlohy Stream Analytics a testování výše dotazu.

````JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
````

## <a name="get-help"></a>Podpora

Potřebujete další pomoc, zkuste naši [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language – referenční informace](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Správa Azure Stream Analytics odkazu k REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
