---
title: Azure Stream Analytics uživatelsky definovaných funkcí jazyka JavaScript
description: V tomto kurzu se provádějí postupy pro pokročilé dotazy, které využívají uživatelem definované funkce jazyka JavaScript.
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: feb0361b460f5b18b5a8aaa585332e2179023458
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851178"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Kurz: Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics
 
Azure Stream Analytics podporuje uživatelem definované funkce, které jsou napsané v jazyce JavaScript. Díky bohaté sadě metod **String**, **RegExp**, **Math**, **Array** a **Date**, které jazyk JavaScript poskytuje, je teď vytváření komplexních transformací dat pomocí úloh Stream Analytics snadnější.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Definovat uživatelem definovanou funkci jazyka JavaScript
> * Přidat funkci na portál
> * Definovat dotaz, který funkci spouští

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="javascript-user-defined-functions"></a>Uživatelem definované funkce jazyka JavaScript
Uživatelem definované funkce jazyka JavaScript podporují bezstavové skalární funkce určené jen pro výpočty, které nevyžadují externí připojení. Návratovou hodnotou funkce může být jenom skalární (jediná) hodnota. Po přidání uživatelem definované funkce jazyka JavaScript do úlohy lze danou funkci použít kdekoli v dotazu jako integrovanou skalární funkci.

Některé scénáře, ve kterých můžou být uživatelem definované funkce jazyka JavaScript užitečné:
* Analýza a manipulace s řetězci, které obsahují funkce s regulárními výrazy, například **Regexp_Replace()** a **Regexp_Extract()**
* Kódování a dekódování dat, například převod z binárního kódování na šestnáctkové kódování
* Provádění matematických výpočtů pomocí funkcí **Math** jazyka JavaScript
* Provádění operací s poli, například seřazení, spojení, hledání a vyplnění

Některé věci, které není možné provádět pomocí uživatelem definované funkce jazyka JavaScript v Stream Analytics:
* Volání externích koncových bodů REST, například provádění zpětného vyhledávání IP adresy nebo načítání referenčních dat z externího zdroje
* Provádění vlastní serializace nebo deserializace formátu událostí u vstupů nebo výstupů
* Vytváření vlastních agregací

Funkce jako **Date.GetDate()** nebo **Math.random()** sice nejsou v definici funkcí blokované, jejich používání byste se ale měli vyhnout. Tyto funkce **nevracejí** při každém volání stejný výsledek a služba Azure Stream Analytics neudržuje deník obsahující záznamy volání funkcí a vrácené výsledky. Pokud funkce vrací při stejných událostech různé výsledky, při restartování úlohy vámi nebo službou Stream Analytics se nezaručuje opakovatelnost.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Přidání uživatelem definované funkce jazyka JavaScript na Azure Portal
Chcete-li vytvořit jednoduchou uživatelem definovanou funkci jazyka JavaScript v rámci existující úlohy Stream Analytics, postupujte takto:

> [!NOTE]
> Tyto kroky fungují na úlohách Stream Analytics nakonfigurovaných pro spouštění v cloudu. Pokud je vaše úloha Stream Analytics nakonfigurovaná tak, aby běžela na Azure IoT Edge, místo toho použijte Visual Studio a [zapište C#uživatelsky definovanou funkci pomocí ](stream-analytics-edge-csharp-udf.md).

1.  Na portálu Azure Portal vyhledejte příslušnou úlohu Stream Analytics.

2. Pod hlavičkou **topologie úlohy** vyberte **funkce**. Zobrazí se prázdný seznam funkcí.

3.  Pokud chcete vytvořit novou uživatelsky definovanou funkci, vyberte **+ Přidat**.

4.  V okně **Nová funkce** jako **Typ funkce** vyberte **JavaScript**. V editoru se zobrazí výchozí šablona funkce.

5.  Jako **UDF alias** (Alias funkce definované uživatelem) zadejte **hex2Int** a změňte implementaci funkce takto:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Vyberte **Save** (Uložit). Daná funkce se zobrazí v seznamu funkcí.
7.  Vyberte novou funkci **hex2Int** a zkontrolujte definici funkce. U všech funkcí je k aliasu funkce přidaná předpona **UDF**. Při volání funkce v dotazu Stream Analytics je nutné *zahrnout předponu*. V tomto případě byste volali **UDF.hex2Int**.

## <a name="testing-javascript-udfs"></a>Testování UDF JavaScriptu 
Logiku JavaScriptu pro systém souborů JavaScript můžete testovat a ladit v jakémkoli prohlížeči. Ladění a testování logiky těchto uživatelsky definovaných funkcí se v současnosti na portálu Stream Analytics nepodporuje. Jakmile funkce funguje podle očekávání, můžete ji přidat do Stream Analytics úlohy, jak je uvedeno výše, a pak ji vyvolat přímo z dotazu.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Volání uživatelem definované funkce jazyka JavaScript v dotazu

1. V editoru dotazů vyberte v záhlaví **topologie úlohy** možnost **dotaz**.
2.  Upravte dotaz a pak volejte uživatelem definovanou funkci, například takto:

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Pokud chcete nahrát ukázkový datový soubor, klikněte pravým tlačítkem myši na vstup úlohy.
4.  Pokud chcete otestovat dotaz, vyberte **Test**.


## <a name="supported-javascript-objects"></a>Podporované objekty jazyka JavaScript
Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics podporují standardní předdefinované objekty jazyka JavaScript. Seznam těchto objektů najdete v tématu [Globální objekty](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Převod typů pro Stream Analytics a JavaScript

Mezi typy podporovanými dotazovacím jazykem Stream Analytics a jazykem JavaScript existují rozdíly. Tato tabulka uvádí mapování převodu mezi těmito dvěma jazyky:

Stream Analytics | JavaScript
--- | ---
bigint | Číslo (JavaScript může používat celá čísla jenom do hodnoty 2^53)
Datum a čas | Datum (JavaScript podporuje jenom milisekundy)
double | Počet
nvarchar(MAX) | String
Záznam | Objekt
Pole | Pole
NULL | Null


Převody z jazyka JavaScript do Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Počet | Bigint (pokud je číslo zaokrouhlené a je v rozsahu long.MinValue a long.MaxValue; jinak typ double)
Datum | Datum a čas
String | nvarchar(MAX)
Objekt | Záznam
Pole | Pole
Null, Nedefinováno | NULL
Jakýkoli jiný typ (například funkce nebo chyba) | Nepodporuje se (výsledkem je chyba za běhu)

Jazyk JavaScript rozlišuje velká a malá písmena a velikost polí objektu v kódu JavaScriptu musí odpovídat velikosti písmen polí v příchozích datech. Pamatujte na to, že úlohy s úrovní kompatibility 1,0 převede pole z příkazu SQL SELECT na malá písmena. V části úroveň kompatibility 1,1 a vyšší budou mít pole z příkazu SELECT stejná velká písmena jako zadaná v dotazu SQL.

## <a name="troubleshooting"></a>Odstraňování potíží
Chyby jazyka JavaScript za běhu se považují za závažné a zobrazují se prostřednictvím protokolu aktivit. Pokud chcete protokol načíst, přejděte na portálu Azure Portal na příslušnou úlohu a vyberte **Protokol aktivit**.

## <a name="other-javascript-user-defined-function-patterns"></a>Další vzory uživatelem definovaných funkcí jazyka JavaScript

### <a name="write-nested-json-to-output"></a>Zápis vnořeného řetězce JSON do výstupu
Pokud máte krok následného zpracování, který jako vstup používá výstup úlohy Stream Analytics a vyžaduje formát JSON, můžete do výstupu zapsat řetězec JSON. V dalším příkladu se volá funkce **JSON.stringify()** , která sbalí všechny dvojice název/hodnota ve vstupu a pak je zapíše jako jedinou hodnotu řetězce do výstupu.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku.  
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="get-help"></a>Podpora
Pokud potřebujete další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili úlohu Stream Analytics, která spouští jednoduchou uživatelem definovanou funkci jazyka JavaScript. Pokud se chcete dozvědět o Stream Analytics více, pokračujte na další články obsahující scénáře z reálného života:

> [!div class="nextstepaction"]
> [Analýza subjektivního hodnocení na Twitteru v reálném čase v Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
