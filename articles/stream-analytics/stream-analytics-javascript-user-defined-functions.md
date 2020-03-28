---
title: Uživatelem definované funkce JavaScriptu Azure Stream Analytics
description: Tento článek je úvodem k uživatelem definovaným funkcím JavaScriptu v Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235393"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Uživatelem definované funkce JavaScriptu v Azure Stream Analytics
 
Azure Stream Analytics podporuje uživatelem definované funkce, které jsou napsané v jazyce JavaScript. Díky bohaté sadě metod **String**, **RegExp**, **Math**, **Array**a **Date,** které JavaScript poskytuje, se usnadňují vytváření složitých transformací dat s úlohami Stream Analytics.

## <a name="overview"></a>Přehled

Uživatelem definované funkce JavaScriptu podporují bezstavové skalární funkce pouze pro výpočty, které nevyžadují externí připojení. Návratovou hodnotou funkce může být jenom skalární (jediná) hodnota. Po přidání uživatelem definované funkce jazyka JavaScript do úlohy lze danou funkci použít kdekoli v dotazu jako integrovanou skalární funkci.

Některé scénáře, ve kterých můžou být uživatelem definované funkce jazyka JavaScript užitečné:
* Analýza a manipulace s řetězci, které obsahují funkce s regulárními výrazy, například **Regexp_Replace()** a **Regexp_Extract()**
* Kódování a dekódování dat, například převod z binárního kódování na šestnáctkové kódování
* Provádění matematických výpočtů s funkcemi JavaScript **Math**
* Operace s polem, jako je řazení, spojení, hledání a vyplňování

Tady je pár věcí, které s uživatelem definovanou funkcí JavaScriptu v Stream Analytics dělat nemůžete:
* Volání externích koncových bodů REST, například zpětnévyhledávání IP nebo vytažení referenčních dat z externího zdroje
* Provádění vlastní serializace nebo deserializace formátu událostí u vstupů nebo výstupů
* Vytváření vlastních agregací

Přestože funkce jako **Date.GetDate()** nebo **Math.random()** nejsou blokovány v definici funkcí, měli byste se vyhnout jejich použití. Tyto funkce **nevrátí** stejný výsledek pokaždé, když jim zavoláte a služba Azure Stream Analytics neuchovává deník vyvolání funkcí a vrácené výsledky. Pokud funkce vrátí jiný výsledek u stejných událostí, opakovatelnost není zaručena, když jste úlohu restartovali vy nebo služba Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Přidání uživatelem definované funkce JavaScriptu do úlohy

> [!NOTE]
> Tyto kroky fungují na úlohách Stream Analytics nakonfigurovaných pro spuštění v cloudu. Pokud je vaše úloha Stream Analytics nakonfigurovaná tak, aby se spouštěla na Azure IoT Edge, použijte místo toho Visual Studio a [napište uživatelem definovanou funkci pomocí jazyka C#](stream-analytics-edge-csharp-udf.md).

Chcete-li v úloze Stream Analytics vytvořit uživatelsky definovanou funkci JavaScriptu, vyberte v části **Topologie úlohy**možnost **Funkce** . Potom vrozené nabídce **+Přidat** vyberte **JavaScript UDF.** 

![Přidat JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

Poté je nutné zadat následující vlastnosti a vybrat **možnost Uložit**.

|Vlastnost|Popis|
|--------|-----------|
|Alias funkce|Zadejte název pro vyvolání funkce v dotazu.|
|Typ výstupu|Zadejte, který bude vrácen uživatelem definovanou funkcí JavaScriptu do vašeho dotazu Stream Analytics.|
|Definice funkce|Implementace funkce JavaScriptu, která bude spuštěna při každém vyvolání udf z vašeho dotazu.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testování a odstraňování potíží s jazykem JavaScript 

Logiku JavaScriptU UDF můžete testovat a ladit v libovolném prohlížeči. Ladění a testování logiky těchto uživatelem definovaných funkcí není aktuálně podporováno na portálu Stream Analytics. Jakmile funkce funguje podle očekávání, můžete ji přidat do úlohy Stream Analytics, jak je uvedeno výše, a pak ji vyvolat přímo z dotazu. Logiku dotazu můžete otestovat pomocí JavaScriptU UDF pomocí [nástrojů Stream Analytics pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

Chyby jazyka JavaScript za běhu se považují za závažné a zobrazují se prostřednictvím protokolu aktivit. Pokud chcete protokol načíst, přejděte na portálu Azure Portal na příslušnou úlohu a vyberte **Protokol aktivit**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Volání uživatelem definované funkce jazyka JavaScript v dotazu

Funkci JavaScript můžete ve svém dotazu snadno vyvolat pomocí aliasu funkce s předponou **udf**. Zde je příklad javascriptového udf, který převádí šestnáctkové hodnoty na celé číslo, které je vyvoláno v dotazu Stream Analytics.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Podporované objekty jazyka JavaScript

Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics podporují standardní předdefinované objekty jazyka JavaScript. Seznam těchto objektů najdete v tématu [Globální objekty](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Převod typů pro Stream Analytics a JavaScript

Mezi typy podporovanými dotazovacím jazykem Stream Analytics a jazykem JavaScript existují rozdíly. Tato tabulka uvádí mapování převodu mezi těmito dvěma jazyky:

Stream Analytics | JavaScript
--- | ---
bigint | Číslo (JavaScript může používat celá čísla jenom do hodnoty 2^53)
DateTime | Datum (JavaScript podporuje jenom milisekundy)
double | Číslo
nvarchar(MAX) | Řetězec
Záznam | Objekt
Pole | Pole
NULL | Null

Převody z jazyka JavaScript do Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Číslo | Bigint (pokud je číslo zaokrouhlené a je v rozsahu long.MinValue a long.MaxValue; jinak typ double)
Datum | DateTime
Řetězec | nvarchar(MAX)
Objekt | Záznam
Pole | Pole
Null, Nedefinováno | NULL
Jakýkoli jiný typ (například funkce nebo chyba) | Nepodporuje se (výsledkem je chyba za běhu)

Jazyk JavaScript rozlišuje malá a velká písmena a velká a malá písmena polí objektů v kódu JavaScriptu se musí shodovat s písmeny polí v příchozích datech. Úlohy s úrovní kompatibility 1.0 převedou pole z příkazu SQL SELECT na malá písmena. V rámci úrovně kompatibility 1.1 a vyšší pole z příkazu SELECT budou mít stejné písmeno, jak je uvedeno v dotazu SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Další vzory uživatelem definovaných funkcí jazyka JavaScript

### <a name="write-nested-json-to-output"></a>Zápis vnořeného řetězce JSON do výstupu

Pokud máte krok následného zpracování, který jako vstup používá výstup úlohy Stream Analytics a vyžaduje formát JSON, můžete do výstupu zapsat řetězec JSON. V dalším příkladu se volá funkce **JSON.stringify()**, která sbalí všechny dvojice název/hodnota ve vstupu a pak je zapíše jako jedinou hodnotu řetězce do výstupu.

**Definice uživatelem definované funkce jazyka JavaScript:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Ukázkový dotaz:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>Další kroky

* [Strojové učení UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
