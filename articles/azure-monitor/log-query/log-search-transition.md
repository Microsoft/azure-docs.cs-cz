---
title: Zobrazení a analýza dat v Azure Log Analytics | Microsoft Docs
description: Pomoc pro uživatele Log Analytics prohledávání protokolu pro Azure Monitor možnosti dotazování protokolu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 8abd9d7f33a07141418ad67cc2128af40ad0bd51
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607336"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Přechod z Log Analyticsho prohledávání protokolu do protokolů Azure Monitor
Hledání protokolu v Log Analytics bylo nedávno nahrazeno novým prostředím pro analýzu Azure Monitor protokolů. Stránka pro prohledávání protokolu je nyní stále přístupná prostřednictvím položky nabídky **protokoly (Classic)** na stránce **Log Analytics pracovní prostory** v Azure Portal, ale bude odebrána 15. února 2019. Tento článek popisuje rozdíly mezi dvěma prostředími, které vám pomůžou při přechodu z prohledávání protokolu. 

## <a name="filter-results-of-a-query"></a>Filtrování výsledků dotazu
V hledání v protokolu se zobrazí seznam filtrů, které budou doručeny do výsledků hledání. Vyberte filtr a kliknutím na **použít** spusťte dotaz s vybraným filtrem.

![Filtr hledání v protokolu](media/log-search-transition/filter-log-search.png)

V Azure Monitor protokoly vyberte *filtr (Preview)* a zobrazte filtry. Kliknutím na ikonu filtru zobrazíte filtry pro přidání. Vyberte filtr a kliknutím na **použít & spustit** spusťte dotaz s vybraným filtrem.

![Filtr protokolů](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrahování vlastních polí 
V části prohledávání protokolu můžete extrahovat [vlastní pole](../platform/custom-fields.md) ze zobrazení seznamu, kde nabídka pole obsahuje pole _extrahovat akce z tabulky_.

![Pole extrakce hledání v protokolu](media/log-search-transition/extract-fields-log-search.png)

V protokolu Azure Monitor můžete extrahovat vlastní pole ze zobrazení tabulky. Rozbalte záznam kliknutím na šipku vlevo a potom klikněte na tři tečky pro přístup k akci _extrahování polí_ .

![Protokoly – pole extrahování](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funkce a skupiny počítačů
Pokud chcete výsledky hledání uložit v hledání v protokolu, vyberte možnost **uložená hledání** a **Přidat** a zadejte název, kategorii a text dotazu. Vytvořte [skupinu počítačů](../platform/computer-groups.md) přidáním aliasu funkce.

![Uložit prohledávání protokolu](media/log-search-transition/save-search-log-search.png)

Pokud chcete uložit aktuální dotaz do protokolů Azure Monitor, vyberte **Uložit**. Změňte **Uložit jako** na _funkci_ a zadejte **alias funkce** pro vytvoření [funkce](functions.md). Pokud chcete použít alias funkce pro [skupinu počítačů](../platform/computer-groups.md), vyberte _Uložit tento dotaz jako skupinu počítačů_ .

![Uložit dotaz na protokol](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Uložené dotazy
V hledání v protokolu jsou uložené dotazy k dispozici prostřednictvím **uloženého hledání**položky panelu akcí. V Azure Monitor protokoly získáte přístup k uloženým dotazům z [Průzkumníku dotazů](./get-started-portal.md#save-queries).

![Průzkumník dotazů](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Přechod k podrobnostem o shrnutých řádcích
V hledání v protokolu můžete kliknout na řádek v souhrnném dotazu a spustit další dotaz, který obsahuje seznam podrobných záznamů v daném řádku.

![Podrobnosti hledání v protokolu](media/log-search-transition/drilldown-search.png)

V protokolu Azure Monitor musíte upravit dotaz, aby se tyto záznamy vracely. Rozbalte jeden z řádků ve výsledcích a kliknutím na tlačítko **+** vedle hodnoty ho přidejte do dotazu. Pak z příkazu **Shrnutí** přidejte komentář a spusťte dotaz znovu.

![Podrobnosti o protokolu Azure Monitor](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Provést akci
V hledání v protokolu můžete [Spustit sadu Runbook](../platform/action-groups.md) z výsledků hledání výběrem **akce provést**.

![Provést akci](media/log-search-transition/take-action-log-search.png)

V Azure Monitor protokoly [vytvořte výstrahu z dotazu protokolu](../platform/alerts-log.md). Nakonfigurujte skupinu akcí s jednou nebo více akcemi, které se spustí v reakci na danou výstrahu.

![Skupina akcí](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o nových [prostředích Azure monitor protokolů](get-started-portal.md).

