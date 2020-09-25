---
title: Uživatelsky definované agregace JavaScriptu v Azure Stream Analytics
description: Tento článek popisuje, jak provádět pokročilé mechanismy dotazů s uživatelsky definovanými agregacemi jazyka JavaScript v Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: 6e43a43d929d256bc2a12b6b243d9620c5bd1aba
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317697"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics uživatelsky definovaných agregací jazyka JavaScript
 
Azure Stream Analytics podporuje uživatelsky definované agregace (UDA) napsané v JavaScriptu, umožňuje implementovat složitou stavovou obchodní logiku. V rámci UDA máte plnou kontrolu nad strukturou stavových dat, akumulací stavu, dekumulací stavu a agregovaným výpočtem výsledků. Tento článek představuje dvě různá rozhraní UDA JavaScriptu, kroky pro vytvoření UDA a použití UDA s operacemi založenými na oknech v Stream Analytics dotazu.

## <a name="javascript-user-defined-aggregates"></a>Uživatelsky definované agregace JavaScriptu

Uživatelsky definovaná agregace se používá na základě specifikace časového okna pro agregaci mezi událostmi v tomto okně a vytvořením jediné hodnoty výsledku. Existují dva typy rozhraní UDA, které Stream Analytics podporuje dnes, AccumulateOnly a AccumulateDeaccumulate. Oba typy UDA lze použít na základě bubnu, skákající, posuvné a okno relace. AccumulateDeaccumulate UDA provádí při použití v kombinaci s skákající, posuvnou a oknem relace lepší lepší než AccumulateOnly UDA. V závislosti na použitém algoritmu zvolíte jeden ze dvou typů.

### <a name="accumulateonly-aggregates"></a>Agregace AccumulateOnly

Agregace AccumulateOnly mohou nashromáždit pouze nové události do svého stavu, algoritmus nepovoluje deakumulaci hodnot. Vyberte tento agregovaný typ při deakumulaci informace události z hodnoty stavu není možné implementovat. Následuje šablona JavaScriptu pro agregace AccumulatOnly:

```JavaScript
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
```

### <a name="accumulatedeaccumulate-aggregates"></a>Agregace AccumulateDeaccumulate

AccumulateDeaccumulate agregace umožňují deakumulaci předchozí akumulované hodnoty ze stavu, například odebrání dvojice klíč-hodnota ze seznamu hodnot události nebo odečtení hodnoty ze stavu agregace Sum. Následuje šablona JavaScriptu pro agregace AccumulateDeaccumulate:

```JavaScript
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
```

## <a name="uda---javascript-function-declaration"></a>Deklarace funkce UDA-JavaScript

Jednotlivé UDAy JavaScriptu jsou definovány deklarací objektu funkce. Níže jsou uvedené hlavní prvky v definici UDA.

### <a name="function-alias"></a>Alias funkce

Alias funkce je UDA identifikátor. Při volání v Stream Analytics dotaz vždy použijte alias UDA spolu s "Uda". „com.microsoft.intune.mam“.

### <a name="function-type"></a>Typ funkce

V případě UDA by měl být typu funkce **JavaScript Uda**.

### <a name="output-type"></a>Typ výstupu

Konkrétní typ, který Stream Analytics úloha podporuje, nebo "any", pokud chcete typ v dotazu zpracovat.

### <a name="function-name"></a>Název funkce

Název tohoto objektu funkce. Název funkce by měl odpovídat aliasu UDA.

### <a name="method---init"></a>Metoda-init ()

Metoda init () inicializuje stav agregace. Tato metoda je volána při spuštění okna.

### <a name="method--accumulate"></a>Metoda – Akumulovaná ()

Metoda akumulace () vypočítá stav UDA na základě předchozího stavu a aktuálních hodnot události. Tato metoda je volána, když událost vstoupí do časového okna (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW nebo SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Metoda – deakumulace ()

Metoda deakumulace () přepočítá stav na základě předchozího stavu a aktuálních hodnot události. Tato metoda je volána, když událost opustí SLIDINGWINDOW nebo SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda – deaccumulateState ()

Metoda deaccumulateState () přepočítá stav na základě předchozího stavu a stavu směrování. Tato metoda je volána, když sada událostí opustí HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda – computeResult ()

Metoda computeResult () vrací agregační výsledek na základě aktuálního stavu. Tato metoda je volána na konci časového okna (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW nebo SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>UDA JavaScript podporuje vstupní a výstupní datové typy
V případě datových typů UDA JavaScriptu, přečtěte si část **Stream Analytics a převod typu JavaScriptu** [Integrujte JavaScript UDF](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Přidání JavaScriptu UDA z Azure Portal

Níže jsme provedli proces vytvoření UDA z portálu. V příkladu, který zde používáme, je výpočetní čas vážený průměr.

Teď vytvoříme UDA JavaScriptu v existující úloze ASA pomocí následujících kroků.

1. Přihlaste se k Azure Portal a vyhledejte stávající Stream Analytics úlohu.
1. Pak klikněte na odkaz funkce v části **topologie úlohy**.
1. Kliknutím na ikonu **Přidat** přidejte novou funkci.
1. V novém zobrazení funkce vyberte v editoru **Uda JavaScript** jako typ funkce a pak se zobrazí výchozí šablona Uda.
1. Jako alias UDA zadejte "TWA" a změňte implementaci funkce následujícím způsobem:

    ```JavaScript
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
    ```

1. Po kliknutí na tlačítko Uložit se UDA zobrazí v seznamu funkcí.

1. Klikněte na novou funkci TWA, kde můžete zjistit definici funkce.

## <a name="calling-javascript-uda-in-asa-query"></a>Volání JavaScriptu UDA v dotazu ASA

V Azure Portal a otevřete svoji úlohu, upravte dotaz a zavolejte funkci TWA () s předponou mandátu "Uda". Příklad:

```SQL
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
```

## <a name="testing-query-with-uda"></a>Testování dotazu pomocí UDA

Vytvořte místní soubor JSON s níže uvedeným obsahem, nahrajte soubor do Stream Analytics úlohy a testujte dotaz výše.

```JSON
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
```

## <a name="get-help"></a>Podpora

Pokud chcete získat další nápovědu, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční dokumentace jazyka Azure Stream Analytics dotazů](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční informace o REST API správy Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
