---
title: Azure Stream Analytics uživatelsky definovaných funkcí jazyka JavaScript
description: Tento článek představuje úvod do uživatelem definovaných funkcí jazyka JavaScript v Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc, devx-track-js
ms.date: 06/16/2020
ms.openlocfilehash: aac85fdab157d581285af91c4c818258a5f1790b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124777"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics
 
Azure Stream Analytics podporuje uživatelem definované funkce, které jsou napsané v jazyce JavaScript. S bohatou sadou metod **řetězců** , **RegExp** , **Math** , **Array** a **Date** , které poskytuje jazyk JavaScript, je snazší vytvořit složitou transformaci dat s Stream Analytics úlohami.

## <a name="overview"></a>Přehled

Uživatelsky definované funkce jazyka JavaScript podporují bezstavové, jenom výpočetní skalární funkce, které nevyžadují externí připojení. Návratovou hodnotou funkce může být jenom skalární (jediná) hodnota. Po přidání uživatelem definované funkce jazyka JavaScript do úlohy lze danou funkci použít kdekoli v dotazu jako integrovanou skalární funkci.

Některé scénáře, ve kterých můžou být uživatelem definované funkce jazyka JavaScript užitečné:
* Analýza a manipulace s řetězci, které obsahují funkce s regulárními výrazy, například **Regexp_Replace()** a **Regexp_Extract()**
* Kódování a dekódování dat, například převod z binárního kódování na šestnáctkové kódování
* Provádění matematickýchch výpočtů pomocí **matematických** funkcí JavaScriptu
* Provádění operací pole jako řazení, spojení, hledání a vyplnění

Tady je několik věcí, které se uživatelsky definovanou funkcí jazyka JavaScript v Stream Analytics nedají provést:
* Volání externích koncových bodů REST, například provádění reverzního vyhledávání IP nebo přijímání referenčních dat z externího zdroje
* Provádění vlastní serializace nebo deserializace formátu událostí u vstupů nebo výstupů
* Vytváření vlastních agregací

I když funkce jako **Date. GETDATE ()** nebo **Math. Random ()** nejsou v definici Functions blokované, neměli byste je používat. Tyto funkce **don't** nevrátí stejný výsledek pokaždé, když je zavoláte, a služba Azure Stream Analytics neudržuje deník vyvolání funkcí a vrácené výsledky. Vrátí-li funkce jiný výsledek pro stejné události, není zaručena opakovatelnost při restartování úlohy vámi nebo službou Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Přidání uživatelem definované funkce jazyka JavaScript do úlohy

> [!NOTE]
> Tyto kroky fungují na úlohách Stream Analytics nakonfigurovaných pro spouštění v cloudu. Pokud je vaše úloha Stream Analytics nakonfigurovaná tak, aby běžela na Azure IoT Edge, místo toho použijte Visual Studio a [zapište uživatelsky definovanou funkci pomocí jazyka C#](stream-analytics-edge-csharp-udf.md).

Pokud chcete vytvořit uživatelsky definovanou funkci JavaScriptu v úloze Stream Analytics, vyberte v části **topologie úlohy** možnost **funkce** . Pak z rozevírací nabídky **+ Přidat** vyberte **JavaScript UDF** . 

![Přidat JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

Pak je nutné zadat následující vlastnosti a vybrat **Uložit** .

|Vlastnost|Popis|
|--------|-----------|
|Alias funkce|Zadejte název pro vyvolání funkce v dotazu.|
|Typ výstupu|Typ, který se vrátí uživatelem definovanou funkcí jazyka JavaScript do dotazu Stream Analytics.|
|Definice funkce|Implementace funkce JavaScriptu, která se spustí pokaždé, když se vaše UDF vyvolá z dotazu.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testování a řešení potíží s JavaScript UDF 

Logiku JavaScriptu pro systém souborů JavaScript můžete testovat a ladit v jakémkoli prohlížeči. Ladění a testování logiky těchto uživatelsky definovaných funkcí se v současnosti na portálu Stream Analytics nepodporuje. Jakmile funkce funguje podle očekávání, můžete ji přidat do Stream Analytics úlohy, jak je uvedeno výše, a pak ji vyvolat přímo z dotazu. Logiku dotazu můžete testovat pomocí JavaScriptu UDF pomocí [Stream Analyticsch nástrojů pro Visual Studio](./stream-analytics-tools-for-visual-studio-install.md).

Chyby jazyka JavaScript za běhu se považují za závažné a zobrazují se prostřednictvím protokolu aktivit. Pokud chcete protokol načíst, přejděte na portálu Azure Portal na příslušnou úlohu a vyberte **Protokol aktivit** .

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Volání uživatelem definované funkce jazyka JavaScript v dotazu

Funkci JavaScriptu můžete v dotazu snadno vyvolat pomocí aliasu funkce s předponou **UDF** . Tady je příklad JavaScriptu UDF, který převede hexadecimální hodnoty na celé číslo vyvolané v Stream Analytics dotazu.

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

Jazyk JavaScript rozlišuje velká a malá písmena a velká a malá písmena polí objektu v kódu JavaScriptu musí odpovídat velikosti písmen polí v příchozích datech. Úlohy s úrovní kompatibility 1,0 budou převádět pole z příkazu SQL SELECT na malá písmena. V části úroveň kompatibility 1,1 a vyšší budou mít pole z příkazu SELECT stejná velká písmena jako zadaná v dotazu SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Další vzory uživatelem definovaných funkcí jazyka JavaScript

### <a name="write-nested-json-to-output"></a>Zápis vnořeného řetězce JSON do výstupu

Pokud máte krok následného zpracování, který jako vstup používá výstup úlohy Stream Analytics a vyžaduje formát JSON, můžete do výstupu zapsat řetězec JSON. V dalším příkladu se volá funkce **JSON.stringify()** , která sbalí všechny dvojice název/hodnota ve vstupu a pak je zapíše jako jedinou hodnotu řetězce do výstupu.

**Definice uživatelem definované funkce jazyka JavaScript:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Vzorový dotaz:**
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

### <a name="cast-string-to-json-object-to-process"></a>Přetypování řetězce na objekt JSON, který se má zpracovat

Máte-li pole řetězce, které je JSON a chcete ho převést na objekt JSON pro zpracování v jazyce JavaScript UDF, můžete použít funkci **JSON. Parse ()** pro vytvoření objektu JSON, který se pak dá použít.

**Definice uživatelem definované funkce jazyka JavaScript:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Vzorový dotaz:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Pro zpracování chyb použít try/catch

Bloky try/catch vám pomůžou identifikovat problémy se špatnými vstupními daty, která se předávají do JavaScriptu UDF.

**Definice uživatelem definované funkce jazyka JavaScript:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Vzorový dotaz: předejte celý záznam jako první parametr, aby se mohl vrátit, pokud dojde k chybě.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

## <a name="next-steps"></a>Další kroky

* [Machine Learning UDF](./machine-learning-udf.md)
* [Uživatelsky definovaná funkce v C#](./stream-analytics-edge-csharp-udf-methods.md)