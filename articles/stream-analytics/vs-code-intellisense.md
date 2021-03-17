---
title: IntelliSense v Azure Stream Analyticsch nástrojích pro Visual Studio Code
description: Tento článek popisuje, jak používat funkce IntelliSense v Azure Stream Analyticsch nástrojích pro Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491566"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense v Azure Stream Analyticsch nástrojích pro Visual Studio Code

Technologie IntelliSense je k dispozici pro [Stream Analytics dotazovací jazyk](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) v [Azure Stream Analytics nástrojích pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). IntelliSense je podpora dokončování kódu, která zahrnuje několik funkcí: seznam členů, informace o parametrech, rychlé informace a dokončování slov. Funkce IntelliSense se někdy nazývají jiné názvy, například "dokončování kódu", "asistent obsahu" a "porady při psaní kódu".

![Ukázka IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Funkce IntelliSense

Funkce technologie IntelliSense v Stream Analyticsch nástrojích pro VS Code jsou napájené pomocí jazykové služby. Služba jazyka analyzuje váš zdrojový kód a poskytuje inteligentní dokončování kódu na základě sémantiky jazyka. Pokud jazyková služba ví možné dokončení, při psaní se automaticky vymění návrhy IntelliSense. Pokud budete pokračovat v psaní, seznam členů, jako jsou proměnné a metody, je filtrován tak, aby obsahoval pouze členy, které obsahují znaky, které jste zadali. Po stisknutí `Tab` kláves nebo `Enter` vloží technologie IntelliSense vybraný člen.

IntelliSense můžete aktivovat v libovolném okně editoru zadáním znaku triggeru, například znaku tečky `.` .

![automatického dokončování IntelliSense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Pomůcka návrhy podporuje CamelCase filtrování. Můžete zadat písmena, která jsou velká v názvu metody, chcete-li návrhy omezit. Například "CRA" rychle zobrazí "createApplication".

### <a name="types-of-completions"></a>Typy dokončení

Stream Analytics nástroje pro VS Code IntelliSense nabízí různé typy doplňování, včetně návrhů na jazykové servery, fragmenty kódu a jednoduchých textových doplňování na základě slov.

|Dokončení     |  Typ       |
| ----- | ------- |
| Klíčová slova | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Název sady dat| `input`, `output`, `intermediate result set`|
| Název sloupce sady dat|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Dokončování názvů

Kromě automatického dokončování klíčových slov Stream Analytics nástroje pro VS Code přečtou seznam vstupů a výstupů úlohy a názvy sloupců ve vašich zdrojích dat, když jsou nakonfigurované. Přípona pamatuje tyto informace, aby poskytovala možnosti dokončování názvů, které jsou užitečné pro zadávání příkazů s malým počtem klávesových úhozů:

Při kódování není nutné Editor opustit, aby provedl vyhledávání na vstupních názvech úloh, názvu výstupu a názvech sloupců. Můžete zachovat kontext, najít potřebné informace, vložit prvky přímo do kódu a nechat IntelliSense vyplnit vaše zadání.

Všimněte si, že je nutné nakonfigurovat buď místní vstup, nebo živý vstup a uložit konfigurační soubor, aby bylo možné použít doplňování názvu.

![dokončování názvů](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informace o parametrech

Možnost **informace o parametrech** technologie IntelliSense otevře seznam parametrů, který poskytuje informace o počtu, názvech a typech parametrů vyžadovaných funkcí. Parametr tučně označuje další parametr, který je vyžadován při zadávání funkce.

Seznam parametrů se zobrazí také pro vnořené funkce. Pokud zadáte funkci jako parametr jiné funkci, seznam parametrů zobrazí parametry pro vnitřní funkci. Jakmile je seznam parametrů vnitřních funkcí dokončený, seznam parametrů se vrátí k zobrazení vnějších parametrů funkce.

![informace o parametrech](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Rychlé informace

Jak poskytuje jazyková služba, můžete zobrazit **rychlé informace** pro každý identifikátor ve vašem kódu. Některé příklady identifikátorů jsou vstupní, výstupní, mezilehlá sada výsledků nebo funkce. Když přesunete ukazatel myši na identifikátor, zobrazí se jeho deklarace v překryvném okně. Zobrazí se vlastnosti a schémata dat pro vstupy, pokud jsou nakonfigurované, a mezilehlé datové sady.

![rychlé informace](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Řešení potíží s technologií IntelliSense

Příčinou tohoto problému je chybějící vstupní konfigurace, která poskytuje data. Můžete zjistit, jestli je [místní vstup](visual-studio-code-local-run.md#define-a-local-input) nebo [živý vstup](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) správně nakonfigurovaný.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)
* [Test Stream Analytics dotazy místně s použitím ukázkových dat pomocí Visual Studio Code](visual-studio-code-local-run.md)
* [Test Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí Visual Studio Code](visual-studio-code-local-run-live-input.md)