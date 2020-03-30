---
title: Uživatelem definované agregace JavaScriptu v Azure Stream Analytics
description: Tento článek popisuje, jak provádět pokročilé analýzy dotazů s uživatelem definovanými agregáty JavaScriptu v Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: c509d174787a58abeee33e039eb7bbbcbcb43f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531730"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Uživatelem definované agregáty JavaScriptu Azure Stream Analytics
 
Azure Stream Analytics podporuje uživatelem definované agregáty (UDA) napsané v JavaScriptu, umožňuje implementovat komplexní stavovou obchodní logiku. V rámci UDA máte plnou kontrolu nad stav datové struktury, akumulace stavu, dekumulace stavu a souhrnné výpočty výsledků. Článek představuje dvě různá rozhraní JavaScript UDA, kroky k vytvoření UDA a jak používat UDA s operacemi založenými na oknech v dotazu Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Uživatelsky definované agregáty JavaScriptu

Uživatelem definovaná agregace se používá nad specifikací časového okna k agregaci událostí v tomto okně a vytvoření jediné výsledné hodnoty. Existují dva typy rozhraní UDA, které Stream Analytics podporuje dnes AccumulateOnly a AccumulateDeaccumulate. Oba typy UDA lze použít Tumbling, Hopping, Posuvné a Session Window. AccumulateDeaccumulate UDA funguje lépe než AccumulateOnly UDA při použití společně s Hopping, Posuvné a Session Window. Můžete zvolit jeden ze dvou typů na základě algoritmu, který používáte.

### <a name="accumulateonly-aggregates"></a>AkumulovatPouze agregace

AkumulačníPouze agregace mohou shromažďovat pouze nové události do svého stavu, algoritmus neumožňuje deakumulaci hodnot. Tento agregační typ zvolte, když není možné implementovat informace o události z hodnoty stavu. Následuje javascriptová šablona pro agregace AccumulatOnly:

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

### <a name="accumulatedeaccumulate-aggregates"></a>Akumulačnídekumulační agregáty

Akumulační agregace umožňují deakumulaci předchozí akumulované hodnoty ze stavu, například odeberte dvojici klíč-hodnota ze seznamu hodnot událostí nebo odečtěte hodnotu ze stavu součtu agregace. Následuje javascriptová šablona pro agregace AccumulateDeaccumulate:

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

## <a name="uda---javascript-function-declaration"></a>UDA - Deklarace funkce JavaScriptu

Každý JavaScript UDA je definován deklarací objektu Function. Níže jsou uvedeny hlavní prvky v definici UDA.

### <a name="function-alias"></a>Alias funkce

Alias funkce je identifikátor UDA. Při volání v dotazu Stream Analytics vždy používejte alias UDA společně s "uda". „com.microsoft.intune.mam“.

### <a name="function-type"></a>Typ funkce

Pro UDA by měl být typem funkce **Javascript UDA**.

### <a name="output-type"></a>Typ výstupu

Konkrétní typ, který stream analytics úloha podporována, nebo "Any", pokud chcete zpracovat typ v dotazu.

### <a name="function-name"></a>Název funkce

Název tohoto objektu Function. Název funkce by měl odpovídat aliasu UDA.

### <a name="method---init"></a>Metoda - init()

Metoda inicializuje stav agregace inicializuje metodu inicializuje. Tato metoda se nazývá při spuštění okna.

### <a name="method--accumulate"></a>Metoda – akumulovat()

Metoda accumulate() vypočítá stav UDA na základě předchozího stavu a aktuálních hodnot událostí. Tato metoda je volána, když událost vstoupí do časového okna (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW nebo SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Metoda – deaccumulateace()

Metoda deaccumulate() přepočítá stav na základě předchozího stavu a aktuálních hodnot událostí. Tato metoda je volána, když událost opustí SLIDINGWINDOW nebo SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda – deaccumulateState()

Metoda deaccumulateState() přepočítá stav na základě předchozího stavu a stavu směrování. Tato metoda se nazývá, když sada událostí opustit HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda – computeResult()

Metoda computeResult() vrátí souhrnný výsledek na základě aktuálního stavu. Tato metoda je volána na konci časového okna (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW nebo SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Vstupní a výstupní datové typy podporované javascriptem UDA
Informace o datových typech JavaScript UDA naleznete v části **Stream Analytics a převod typu JavaScript** [u integrovat javascriptové ufls](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Přidání JavaScript UDA z portálu Azure

Níže procházíme procesem vytváření UDA z portálu. Zde používáme příklad výpočtu časově vážených průměrů.

Nyní vytvoříme JavaScript UDA pod existující úlohou ASA pomocí následujících kroků.

1. Přihlaste se na portál Azure portal a vyhledejte stávající úlohu Stream Analytics.
1. Poté klikněte na odkaz funkce v části **TOPOLOGIE ÚLOHY**.
1. Kliknutím na ikonu **Přidat** přidáte novou funkci.
1. V zobrazení Nová funkce vyberte jako typ funkce **JavaScript UDA** a pak se v editoru zobrazí výchozí šablona UDA.
1. Vyplňte "TWA" jako alias UDA a změňte implementaci funkce takto:

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

1. Jakmile kliknete na tlačítko "Uložit", vaše UDA se zobrazí v seznamu funkcí.

1. Klikněte na novou funkci "TWA", můžete zkontrolovat definici funkce.

## <a name="calling-javascript-uda-in-asa-query"></a>Volání JavaScript UDA v asa dotazu

Na webu Azure Portal a otevřete úlohu, upravte dotaz a volejte funkci TWA() s předponou mandátu "uda". Například:

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

## <a name="testing-query-with-uda"></a>Testovací dotaz pomocí UDA

Vytvořte místní soubor JSON s obsahem pod, nahrajte jej do úlohy Stream Analytics a vyzkoušejte výše uvedený dotaz.

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

Pokud potřebujete další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k dotazovacímu jazyku Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odkaz na rozhraní REST API správy Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
