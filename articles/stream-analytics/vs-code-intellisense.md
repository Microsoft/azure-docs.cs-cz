---
title: Technologie IntelliSense v nástrojích Azure Stream Analytics pro kód Visual Studia
description: Tento článek popisuje, jak používat funkce IntelliSense v nástrojích Azure Stream Analytics pro kód Visual Studia.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394394"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>Technologie IntelliSense v nástrojích Azure Stream Analytics pro kód Visual Studia

Technologie IntelliSense je k dispozici pro [dotazovací jazyk Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) v [nástrojích Azure Stream Analytics pro kód VS](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). IntelliSense je podpora pro dokončení kódu, která obsahuje řadu funkcí: Seznam členů, Informace o parametrech, Rychlé informace a Kompletní word. Funkce Technologie IntelliSense jsou někdy nazývány jinými názvy, například "dokončení kódu", "pomoc s obsahem" a "nápověda k kódu".

![Ukázka technologie IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Funkce technologie IntelliSense

Funkce Technologie IntelliSense v nástrojích Stream Analytics pro Kód VS jsou poháněny jazykovou službou. Služba jazyka analyzuje zdrojový kód a poskytuje inteligentní dokončování kódu na základě sémantiky jazyka. Pokud jazyková služba zná možné dokončení, při psaní se objeví návrhy technologie IntelliSense. Pokud budete pokračovat v psaní, seznam členů, například proměnné a metody, je filtrován tak, aby zahrnoval pouze členy, které obsahují zadané znaky. Když stisknete klávesy `Tab` nebo, `Enter` intelliSense vloží vybraný člen.

Technologie IntelliSense můžete spustit v libovolném okně editoru zadáním `.`znaku aktivační události, například znaktečky .

![automatické dokončování funkce intellisense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Widget návrhy podporuje filtrování CamelCase. Můžete zadat písmena, která jsou velkými písmeny v názvu metody, abyste návrhy omezili. Například "cra" rychle vyvolá "createApplication".

### <a name="types-of-completions"></a>Typy kompletací

Nástroje Stream Analytics pro technologie VS Code IntelliSense nabízejí různé typy dokončení, včetně návrhů jazykových serverů, úryvků a jednoduchých textových dokončení založených na textu.

|       |         |       |
| ----- | ------- | ----- |
| Klíčová slova | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Název sady dat| `input`, `output`, `intermediate result set`|
| Název sloupce sady dat|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Dokončení názvu

Kromě automatického dokončování klíčových slov nástroje Stream Analytics pro Kód VS přečte seznam vstupních a výstupních názvů úloh, stejně jako názvy sloupců ve zdrojích dat, když jsou nakonfigurovány. Rozšíření si pamatuje tyto informace poskytnout funkce dokončení názvu, které jsou užitečné pro zadávání příkazů s několika úhozy kláves:

Při kódování nemusíte ponechat editor, abyste mohl provádět vyhledávání na názvech vstupů úloh, výstupních jménech a názvech sloupců. Můžete zachovat svůj kontext, najít potřebné informace, vložit prvky přímo do kódu a nechat IntelliSense dokončit psaní za vás.

Všimněte si, že je třeba nakonfigurovat místní vstup nebo živý vstup a uložit konfigurační soubor, aby bylo možné použít dokončení názvu.

![dokončení názvu](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informace o parametrech

Možnost **Informace o parametrech** technologie IntelliSense otevře seznam parametrů, který obsahuje informace o počtu, názvech a typech parametrů, které funkce vyžaduje. Parametr tučným písmem označuje další parametr, který je vyžadován při psaní funkce.

Seznam parametrů je také zobrazen pro vnořené funkce. Pokud zadáte funkci jako parametr do jiné funkce, seznam parametrů zobrazí parametry pro vnitřní funkci. Poté, když je seznam parametrů vnitřní funkce dokončen, seznam parametrů se vrátí k zobrazení parametrů vnější funkce.

![informace o parametru](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Rychlé informace

Podle informací služby jazyka můžete zobrazit **rychlé informace pro** každý identifikátor ve vašem kódu. Některé příklady identifikátorů jsou input, output, a intermediate result set nebo function. Když přesunete ukazatel myši nad identifikátor, jeho deklarace se zobrazí v rozbalovacím okně. Vlastnosti a schémata dat pro vstupy, pokud jsou nakonfigurovány, a zprostředkující sada dat jsou zobrazeny.

![rychlé informace](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Poradce při potížích s technologiemi IntelliSense

Tento problém je způsoben chybějící vstupní konfigurace, která poskytuje data. Můžete zkontrolovat, zda byl [místní vstup](visual-studio-code-local-run.md#define-a-local-input) nebo [živý vstup](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) nakonfigurován správně.

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vytvoření úlohy Azure Stream Analytics v kódu Visual Studia](quick-create-vs-code.md)
* [Test Stream Analytics dotazy místně s ukázkovými daty pomocí kódu Sady Visual Studio](visual-studio-code-local-run.md)
* [Test Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí kódu Sady Visual Studio](visual-studio-code-local-run-live-input.md)
