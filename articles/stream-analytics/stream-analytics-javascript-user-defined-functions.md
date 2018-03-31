---
title: Uživatelem definované funkce Azure Stream Analytics JavaScript | Microsoft Docs
description: Provedení mechanismy rozšířený dotaz s uživatelem definované funkce jazyka JavaScript
keywords: JavaScript, funkce udf definované uživatelem
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: f2b14029ebea7f9cf1fa74a384ecbb72b08b7ad6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Uživatelem definované funkce Azure Stream Analytics JavaScript
Azure Stream Analytics podporuje uživatelsky definované funkce, které jsou napsané v jazyce JavaScript. S bohaté sada **řetězec**, **RegExp**, **matematické**, **pole**, a **datum** metody této JavaScript poskytuje, transformace komplexní dat pomocí služby Stream Analytics bude snazší k vytvoření úlohy.

## <a name="javascript-user-defined-functions"></a>Uživatelem definované funkce jazyka JavaScript
Uživatelem definované funkce jazyka JavaScript podporovat bezstavové, pouze výpočetní skalární funkce, které nevyžadují externí připojení. Návratová hodnota funkce lze pouze skalární hodnotu (jeden). Po přidání uživatelem definované funkce jazyka JavaScript do úlohy, můžete použít funkci kdekoli v dotazu, jako je integrovaná skalární funkce.

Tady je několik scénářů, kde mohou být užitečné JavaScript uživatelsky definované funkce:
* Analýza a manipulace s řetězci, které mají regulární výraz funkce, například **Regexp_Replace()** a **Regexp_Extract()**
* Kódování dat, například binární šestnáctkově převod a dekódování
* Provádění mathematic výpočtů v jazyce JavaScript **matematické** funkce
* Provádění operací pole jako řazení, spojení, najít a výplně

Zde jsou některé věci, které nemůžete dělat s uživatelem definované funkce jazyka JavaScript v Stream Analytics:
* Volání na externí koncové body REST, například provádění obrátíte vyhledávání IP nebo vyžádání referenční data z externího zdroje
* Deserializace na vstupy/výstupy nebo provést vlastní události formát serializace
* Vytvořte vlastní agregace

I když funguje jako **Date.GetDate()** nebo **Math.random()** nejsou blokována v definici funkce byste neměli používat je. Tyto funkce **nepodporují** pokaždé, když je volají a službu Azure Stream Analytics nezachovat deník volání funkce vrátí stejné výsledky a vrátí výsledky. Pokud funkce vrátí výsledek různých na stejné události, opakovatelnost není zaručena při restartování úlohy vy nebo pomocí služby Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Přidat JavaScript uživatelsky definované funkce na portálu Azure
Pokud chcete vytvořit jednoduché funkce jazyka JavaScript uživatelem definované v části existující úlohy Stream Analytics, proveďte tyto kroky:

1.  Na portálu Azure najdete úlohu služby Stream Analytics.
2.  V části **úlohy TOPOLOGIE**, vyberte funkce. Zobrazí se prázdný seznam funkcí.
3.  Chcete-li vytvořit nové uživatelsky definované funkce, vyberte **přidat**.
4.  Na **novou funkci** okně pro **typ funkce**, vyberte **JavaScript**. Funkce výchozí šablona služby se zobrazí v editoru.
5.  Pro **UDF alias**, zadejte **hex2Int**a změňte implementaci funkce následujícím způsobem:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Vyberte **Uložit**. Funkce se zobrazí v seznamu funkcí.
7.  Vyberte novou **hex2Int** funkce a zkontrolujte definici funkce. Mají všechny funkce **UDF** předponu přidat do funkce alias. Budete muset *obsahovat předponu* při volání funkce v dotazu Stream Analytics. V takovém případě volání **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Volání uživatelem definované funkce JavaScript v dotazu

1. V editoru dotazů v rámci **úlohy TOPOLOGIE**, vyberte **dotazu**.
2.  Upravit dotaz a pak zavolají uživatelsky definované funkce, například takto:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Pokud chcete nahrát ukázkový datový soubor, klikněte pravým tlačítkem na vstupu úlohy.
4.  Chcete-li otestovat dotazu, vyberte **testování**.


## <a name="supported-javascript-objects"></a>Podporované JavaScript – objekty
Uživatelem definované funkce Azure Stream Analytics JavaScript podporují standard, předdefinovaných objektů jazyka JavaScript. Seznam těchto objektů najdete v tématu [globální objekty](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics a JavaScript typ – převod

Existují rozdíly v typech, Stream Analytics query language a podporu jazyka JavaScript. Tato tabulka uvádí převod mapování mezi těmito dvěma:

Stream Analytics | JavaScript
--- | ---
bigint | Číslo (JavaScript může představovat jenom celá čísla přesně 2 ^ 53)
DateTime | Datum (JavaScript pouze podporuje v milisekundách)
Double | Číslo
nvarchar(MAX) | Řetězec
Záznam | Objekt
Pole | Pole
NULL | Null


Zde jsou převody JavaScript Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Číslo | Bigint (Pokud je číslo se zaokrouhlí a mezi dlouho. MinValue a dlouho. MaxValue; jinak je dvojité)
Datum | DateTime
Řetězec | nvarchar(MAX)
Objekt | Záznam
Pole | Pole
Hodnotu Null, Nedefinovaná | NULL
Jiný typ (například funkce nebo chyba) | Není podporované (výsledky v chybě za běhu)

## <a name="troubleshooting"></a>Řešení potíží
Chyby jazyka JavaScript runtime jsou považovány za kritické a jsou prezentované prostřednictvím protokolu aktivit. Načtení protokolu, na portálu Azure přejděte na úlohu a vyberte **protokol aktivit**.


## <a name="other-javascript-user-defined-function-patterns"></a>Dalšími vzory uživatelsky definované funkce jazyka JavaScript

### <a name="write-nested-json-to-output"></a>Zápis vnořené JSON na výstup
Pokud máte následné zpracování krok, který používá úloha Stream Analytics výstup jako vstup a vyžaduje formátu JSON, můžete napsat do výstupního řetězce formátu JSON. Další příklad volání **JSON.stringify()** funkci, aby se pack všechny dvojice název/hodnota vstupu a zapsat je jako jeden řetězec hodnotu ve výstupu.

**Definice JavaScript uživatelsky definované funkce:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Ukázkový dotaz:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Podpora
Potřebujete další pomoc, zkuste naši [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language – referenční informace](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Správa Azure Stream Analytics odkazu k REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
