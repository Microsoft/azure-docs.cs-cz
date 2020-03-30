---
title: Diagnostika funkcí Azure – přehled
description: Zjistěte, jak můžete řešit problémy s vaší funkční aplikací pomocí diagnostiky Funkcí Azure.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834036"
---
# <a name="azure-functions-diagnostics-overview"></a>Přehled diagnostiky funkcí Azure

Když používáte aplikaci funkce, chcete být připraveni na všechny problémy, které mohou vzniknout, od chyb 4xx až po selhání. Diagnostika funkcí Azure je inteligentní a interaktivní prostředí, které vám pomůže vyřešit potíže s vaší funkční aplikací bez konfigurace nebo dodatečných nákladů. Když narazíte na problémy s vaší funkční aplikací, diagnostika Azure Functions poukazuje na to, co je špatné, aby vás navedla k správným informacím snadněji a rychleji řešit a vyřešit problém. Tento článek ukazuje základy, jak používat diagnostiku funkcí Azure k rychlejší diagnostice a řešení běžných problémů s aplikacemi funkcí.

## <a name="start-azure-functions-diagnostics"></a>Spuštění diagnostiky funkcí Azure

Přístup k diagnostice funkcí Azure:

1. Přejděte do aplikace funkce na [webu Azure Portal](https://portal.azure.com).
2. Vyberte kartu **Prvky platformy.**
3. Vyberte **Diagnostikovat a řešit problémy** v části **Správa prostředků**, která otevře diagnostiku funkcí Azure.
4. Zvolte kategorii, která nejlépe vystichne problém vaší aplikace funkcí pomocí klíčových slov na dlaždici domovské stránky. Můžete také zadat klíčové slovo, které nejlépe vystichne váš problém na vyhledávacím panelu. Můžete například zadat `execution` seznam diagnostických sestav souvisejících s prováděním aplikace funkce a otevřít je přímo z domovské stránky.

![Domovská stránka](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Použití interaktivního rozhraní

Jakmile vyberete kategorii domovské stránky, která nejlépe odpovídá problému vaší aplikace funkce, interaktivní rozhraní diagnostiky Azure Functions, Genie, vás může provést diagnostikou a řešením problému vaší aplikace. Pomocí klávesových zkratek dlaždic, které genie poskytuje, můžete zobrazit úplnou diagnostickou zprávu o kategorii problémů, která vás zajímá. Klávesové zkratky dlaždic poskytují přímý způsob přístupu k diagnostickým metrikám.

![Džin](./media/functions-diagnostics/genie.png)

Po výběru dlaždice se zobrazí seznam témat souvisejících s problémem popsaným v dlaždici. Tato témata obsahují úryvky pozoruhodných informací z celé sestavy. Můžete vybrat libovolné z těchto témat, abyste problémy dále prozkoumali. Můžete také vybrat **zobrazit celou sestavu** a prozkoumat všechna témata na jedné stránce.

![Náhled diagnostické sestavy](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Zobrazení diagnostické sestavy

Po výběru tématu můžete zobrazit diagnostickou sestavu specifickou pro vaši aplikaci funkcí. Diagnostické sestavy používají stavové ikony k označení, jestli máte s vaší aplikací nějaké konkrétní problémy. Zobrazí se podrobný popis problému, doporučených akcí, souvisejících metrik a užitečných dokumentů. Přizpůsobené diagnostické sestavy jsou generovány z řady kontrol spuštěných v aplikaci funkcí. Diagnostické sestavy mohou být užitečným nástrojem pro určení problémů v aplikaci funkcí a nasměrování k vyřešení problému.

## <a name="find-the-problem-code"></a>Najít kód problému

Pro funkce založené na skriptech můžete použít **spuštění funkce a chyby** v části **Funkce App Down nebo Hlášení chyb** zúžit na řádku kódu způsobuje výjimky nebo chyby. Tato funkce může být užitečným nástrojem pro získání hlavní příčiny a opravu problémů z určitého řádku kódu. Tato možnost není k dispozici pro předkompilované funkce Jazyka C# a Jazyka Java.

![Diagnostická zpráva o chybách spuštění funkce](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Výjimka funkce](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Další kroky

Můžete klást otázky nebo poskytnout zpětnou vazbu k diagnostice Funkcí Azure na [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Uveďte `[Diag]` prosím do názvu své zpětné vazby.

> [!div class="nextstepaction"]
> [Sledování funkčních aplikací](functions-monitoring.md)
