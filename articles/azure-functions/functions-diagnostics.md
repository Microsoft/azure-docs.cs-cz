---
title: Přehled diagnostiky Azure Functions
description: Přečtěte si, jak můžete řešit problémy s aplikací Function App pomocí Azure Functions Diagnostics.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "83122357"
---
# <a name="azure-functions-diagnostics-overview"></a>Přehled diagnostiky ve službě Azure Functions

Když spouštíte aplikaci Function App, chcete se připravit na případné problémy, které mohou nastat, od chyb 4xx, aby se chyby aktivovaly. Diagnostika Azure Functions je inteligentní a interaktivní prostředí, které vám může pomoct při odstraňování potíží s aplikací Function App bez jakýchkoli konfigurací nebo dalších nákladů. Když narazíte na problémy s aplikací Function App, Azure Functions Diagnostika ukazuje, co je špatné. Zobrazí se vám správné informace pro snazší a rychlé řešení potíží a vyřešení problému. V tomto článku se dozvíte základy použití diagnostiky Azure Functions pro rychlejší diagnostiku a řešení běžných potíží s aplikacemi Function App.

## <a name="start-azure-functions-diagnostics"></a>Spustit diagnostiku Azure Functions

Spuštění diagnostiky Azure Functions:

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace Function App.
1. Chcete-li otevřít diagnostiku Azure Functions, vyberte možnost **diagnostikovat a vyřešit problémy** .
1. Vyberte kategorii, která nejlépe popisuje problém vaší aplikace Function pomocí klíčových slov na dlaždici domovské stránky. Můžete také zadat klíčové slovo, které nejlépe popisuje váš problém na panelu hledání. Můžete například zadat, `execution` Chcete-li zobrazit seznam diagnostických sestav souvisejících s prováděním aplikace Function App a otevřít je přímo z domovské stránky.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Vyhledejte diagnostiku Azure Functions." border="true":::

## <a name="use-the-interactive-interface"></a>Použití interaktivního rozhraní

Jakmile vyberete kategorii domovské stránky, která nejlépe odpovídá vašemu problému aplikace Function App, Azure Functions interaktivní rozhraní s názvem Genie, může vás diagnostikovat a řešit problém vaší aplikace. Pomocí klávesových zkratek, které poskytuje Genie, můžete zobrazit úplnou diagnostickou sestavu kategorie problému, na kterou vás zajímáte. Zástupci dlaždic poskytují přímý způsob přístupu k diagnostickým metrikám.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie je Azure Functions rozhraní diagnostiky." border="false":::

Po výběru dlaždice se zobrazí seznam témat souvisejících s problémem popsaným na dlaždici. Tato témata poskytují fragmenty důležitých informací z celé sestavy. Výběrem kterékoli z těchto témat můžete prozkoumat další problémy. Můžete také vybrat **Zobrazit úplnou sestavu** a prozkoumat všechna témata na jedné stránce.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Náhled diagnostické sestavy" border="false":::

## <a name="view-a-diagnostic-report"></a>Zobrazit diagnostickou sestavu

Po výběru tématu si můžete zobrazit diagnostickou zprávu specifickou pro vaši aplikaci Function App. Diagnostické sestavy používají ikony stavu k označení, zda existují konkrétní problémy s vaší aplikací. Zobrazí se podrobný popis problému, doporučené akce, související metriky a užitečné dokumenty. Přizpůsobené diagnostické zprávy se generují z řady kontrol spuštěných ve vaší aplikaci Function App. Diagnostické sestavy můžou být užitečným nástrojem pro zakládání problémů v aplikaci Function App a při řešení tohoto problému.

## <a name="find-the-problem-code"></a>Vyhledejte kód problému.

V případě funkcí založených na skriptech můžete použít **spouštění funkcí a chyby** v části **Function App dolů nebo hlášení chyb** a zúžit tak rozsah kódu, který způsobuje výjimky nebo chyby. Pomocí tohoto nástroje můžete získat původní příčinu a opravit problémy z konkrétního řádku kódu. Tato možnost není k dispozici pro předkompilované funkce C# a Java.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Diagnostická zpráva o chybách spuštění funkce" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Zobrazení podrobností o výjimce." border="false":::

## <a name="next-steps"></a>Další kroky

Můžete klást otázky nebo poskytovat zpětnou vazbu k Azure Functions diagnostice na webu [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Zahrňte `[Diag]` do titulu zpětné vazby.

> [!div class="nextstepaction"]
> [Monitorování aplikací Function App](functions-monitoring.md)
