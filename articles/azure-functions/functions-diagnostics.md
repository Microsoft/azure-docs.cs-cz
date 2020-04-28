---
title: Přehled diagnostiky Azure Functions
description: Přečtěte si, jak můžete řešit problémy s aplikací Function App pomocí Azure Functions Diagnostics.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834036"
---
# <a name="azure-functions-diagnostics-overview"></a>Přehled diagnostiky Azure Functions

Když spouštíte aplikaci Function App, chcete se připravit na případné problémy, které mohou nastat, od chyb 4xx, aby se chyby aktivovaly. Diagnostika Azure Functions je inteligentní a interaktivní prostředí, které vám může pomoct při odstraňování potíží s aplikací Function App bez jakýchkoli konfigurací nebo dalších nákladů. Když narazíte na problémy s aplikací Function App, Azure Functions Diagnostika ukazuje, co je špatné, abyste vás mohli snadno a rychle vyřešit a vyřešit problém. V tomto článku se dozvíte základy použití diagnostiky Azure Functions pro rychlejší diagnostiku a řešení běžných potíží s aplikacemi Function App.

## <a name="start-azure-functions-diagnostics"></a>Spustit diagnostiku Azure Functions

Přístup k diagnostice Azure Functions:

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace Function App.
2. Vyberte kartu **funkce platformy** .
3. Vyberte možnost **diagnostikovat a vyřešit problémy** pod **správou prostředků**, která otevře diagnostiku Azure Functions.
4. Vyberte kategorii, která nejlépe popisuje problém vaší aplikace Function pomocí klíčových slov na dlaždici domovské stránky. Můžete také zadat klíčové slovo, které nejlépe popisuje váš problém na panelu hledání. Můžete například zadat `execution` , chcete-li zobrazit seznam diagnostických sestav souvisejících s prováděním aplikace Function App a otevřít je přímo z domovské stránky.

![Domovská stránka](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Použití interaktivního rozhraní

Jakmile vyberete kategorii domovské stránky, která nejlépe odpovídá vašemu problému aplikace Function App, Azure Functions interaktivní rozhraní Genie Diagnostics, které vám pomůže s diagnostikou a řešením problémů s vaší aplikací. Pomocí klávesových zkratek, které poskytuje Genie, můžete zobrazit úplnou diagnostickou sestavu kategorie problému, o které se zajímáte. Zástupci dlaždic poskytují přímý způsob přístupu k diagnostickým metrikám.

![Genie](./media/functions-diagnostics/genie.png)

Po výběru dlaždice se zobrazí seznam témat souvisejících s problémem popsaným na dlaždici. Tato témata poskytují fragmenty důležitých informací z celé sestavy. Můžete vybrat kterékoli z těchto témat a prozkoumat tak problémy. Můžete také vybrat **Zobrazit úplnou sestavu** a prozkoumat všechna témata na jedné stránce.

![Náhled diagnostické sestavy](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Zobrazit diagnostickou sestavu

Po výběru tématu si můžete zobrazit diagnostickou zprávu specifickou pro vaši aplikaci Function App. Diagnostické sestavy používají ikony stavu k označení, zda existují konkrétní problémy s vaší aplikací. Zobrazí se podrobný popis problému, doporučené akce, související metriky a užitečné dokumenty. Přizpůsobené diagnostické zprávy se generují z řady kontrol spuštěných ve vaší aplikaci Function App. Diagnostické sestavy můžou být užitečným nástrojem pro zakládání problémů v aplikaci Function App a při řešení tohoto problému.

## <a name="find-the-problem-code"></a>Vyhledejte kód problému.

V případě funkcí založených na skriptech můžete použít **spouštění funkcí a chyby** v části **Function App dolů nebo hlášení chyb** a zúžit tak rozsah kódu, který způsobuje výjimky nebo chyby. Tato funkce může být užitečným nástrojem pro získání hlavní příčiny a řešení problémů z konkrétního řádku kódu. Tato možnost není k dispozici pro předkompilované funkce C# a Java.

![Diagnostická zpráva o chybách spuštění funkce](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Výjimka funkce](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Další kroky

Můžete klást otázky nebo poskytovat zpětnou vazbu k Azure Functions diagnostice na webu [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Uveďte `[Diag]` prosím název své zpětné vazby.

> [!div class="nextstepaction"]
> [Monitorování aplikací Function App](functions-monitoring.md)
