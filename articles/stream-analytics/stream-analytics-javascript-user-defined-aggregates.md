---
title: Javascriptové uživatelsky definované agregace v Azure Stream Analytics
description: Tento článek popisuje, jak provádět mechanics rozšířený dotaz pomocí jazyka JavaScript, uživatelem definované agregace v Azure Stream Analytics.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: bdf5b5188dd584c5eb20f72ff4a98ba6904bc53e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702370"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics JavaScript uživatelsky definované agregace (Preview)
 
Azure Stream Analytics podporuje uživatelem definované agregace (UDA) napsané v jazyce JavaScript, umožňuje implementovat komplexní stavové obchodní logiku. V UDA máte plnou kontrolu stavu datová struktura, akumulací stav, stav dekumulaci a agregační výsledek výpočtu. Tento článek přináší dvě různé UDA JavaScriptu Implementovat rozhraní, postup vytvoření UDA a jak pomocí UDA se systémem Windows operace v dotazu Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Uživatelem definované agregace jazyka JavaScript

Uživatelem definovaná agregace se používá na časový rozsah okna pro agregaci událostí v tomto okně a vytvoří jednu výslednou hodnotu. Existují dva typy rozhraní UDA, Stream Analytics podporuje v současné době AccumulateOnly a AccumulateDeaccumulate. Oba typy UDA můžete využívat Přeskakujícího okna, kdy se skok provádí okno a posuvné okno. AccumulateDeaccumulate UDA vrací lepší výsledky než AccumulateOnly UDA spolupráci s dosáhneme okno a posuvné okno. Vyberte jeden ze dvou typů podle algoritmus, který používáte.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly agregace

Agregace AccumulateOnly lze nashromáždit pouze nové události do stavu, algoritmus neumožňuje deaccumulation hodnot. Vyberte tento požadovaný typ agregace při deaccumulate událost informace z hodnoty stavu je možné implementovat. Toto je šablona jazyka JavaScript pro AccumulatOnly agregace:

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

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate agregace

Agregace AccumulateDeaccumulate povolí deaccumulation předchozí celkové hodnoty ze stavu, například, odebrat ze seznamu událostí hodnoty pár klíč hodnota nebo odečíst hodnotu ze stavu agregace Sum. Toto je šablona jazyka JavaScript pro AccumulateDeaccumulate agregace:

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

Každý UDA JavaScriptu je definována deklaraci objektu funkce. Toto jsou hlavními prvky ve UDA definice.

### <a name="function-alias"></a>Alias funkce

Alias funkce je identifikátor UDA. Při volání v dotazu Stream Analytics, vždy používejte alias UDA spolu s "uda". Předpona.

### <a name="function-type"></a>Typ funkce

UDA, by měl být typu funkce **UDA JavaScriptu Implementovat**.

### <a name="output-type"></a>Typ výstupu

Konkrétní typ úlohy Stream Analytics nepodporuje, nebo "Libovolné" Pokud chcete zpracovat typ v dotazu.

### <a name="function-name"></a>Název funkce

Název tohoto objektu funkce. Název funkce by měl odpovídat doslova UDA alias (ve verzi preview chování, jsme zvažuje anonymní funkce podpory při všeobecné dostupnosti).

### <a name="method---init"></a>Metoda - init()

Metoda init() inicializuje stav agregace. Tato metoda je volána, když se otevře se okno.

### <a name="method--accumulate"></a>Metoda – accumulate()

Metoda accumulate() počítá UDA stavu na základě předchozího stavu a aktuální hodnoty událostí. Tato metoda je volána, když událost zadá časový interval (TUMBLINGWINDOW, HOPPINGWINDOW nebo SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Metoda – deaccumulate()

Metoda deaccumulate() přepočítá stavu na základě předchozího stavu a aktuální hodnoty událostí. Tato metoda je volána, když událost opustí SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda – deaccumulateState()

Metoda deaccumulateState() přepočítá stavu na základě předchozího stavu a stavu směrování. Tato metoda je volána, když sada nechte události a HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda – computeResult()

Metoda computeResult() vrátí agregace výsledků na základě aktuálního stavu. Tato metoda je volána na konci časový interval (TUMBLINGWINDOW HOPPINGWINDOW a SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>UDA JavaScriptu Implementovat podporované typy vstupní a výstupní data
Typy dat UDA JavaScriptu Implementovat, najdete v části **Stream Analytics a JavaScript převod typu** z [integrace funkcí UDF JavaScriptu](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Přidání UDA JavaScriptu na webu Azure Portal

Níže najdete provedeme procesem vytvoření UDA z portálu. V příkladu, které zde používáme je computing časově vážené průměry.

Teď vytvoříme UDA JavaScriptu v rámci existující úlohy Azure Stream Analytics podle následujících kroků.

1. Přihlaste se k webu Azure portal a vyhledejte existující úlohy Stream Analytics.
1. Klikněte na odkaz funkce v rámci **TOPOLOGIE úlohy**.
1. Klikněte na **přidat** ikona pro přidání nové funkce.
1. Na novou funkci zobrazení, vyberte **UDA JavaScriptu Implementovat** jako typ funkce, pak se zobrazí výchozí šablona UDA zobrazí v editoru.
1. Zadejte "TWA" jako UDA alias a změňte implementaci funkce takto:

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

1. Po kliknutí na tlačítko "Save" vaší UDA se zobrazí v seznamu funkcí.

1. Klikněte na novou funkci "TWA", můžete zkontrolovat definici funkce.

## <a name="calling-javascript-uda-in-asa-query"></a>Volání UDA JavaScriptu Implementovat v dotazu Azure Stream Analytics

Na webu Azure portal a otevřete úlohu, upravte dotaz a volání funkce TWA() s předponou pověření "uda.". Příklad:

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

## <a name="testing-query-with-uda"></a>Testování dotazu s UDA

Vytvořte místní soubor JSON s níže obsah, nahrajte soubor do úlohy Stream Analytics a testovat dotaz výše.

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

Pokud potřebujete další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční dokumentace jazyka dotazu Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Pro správu Azure Stream Analytics reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
