---
title: Zobrazení a analýza dat v Azure Log Analytics | Dokumentace Microsoftu
description: Pomoc uživatelům prohledávání protokolu Log Analytics do prostředí Azure Monitor protokolu dotazu.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751357"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Přechod z prohledávání protokolu log Analytics do protokoly Azure monitoru
Prohledávání protokolů v Log Analytics se nedávno nahrazena nové prostředí pro analýzy protokolů Azure Monitor. Na stránce vyhledávání protokolu je aktuálně stále přístupné prostřednictvím **protokoly (classic)** položky nabídky v **pracovních prostorů Log Analytics** stránky v Azure portal, ale odebere 15. února 2019. Tento článek popisuje rozdíly mezi oběma prostředími usnadňují přechod z prohledávání protokolů. 

## <a name="filter-results-of-a-query"></a>Filtrování výsledků dotazu
V prohledávání protokolu se zobrazí seznam filtrů, jako jsou doručeny výsledky hledání. Vyberte filtr a klikněte na tlačítko **použít** spusťte dotaz s filtrem vybrané.

![Vyhledávací filtr protokolu](media/log-search-transition/filter-log-search.png)

V protokolech Azure Monitor, vyberte *filtru (preview)* pro zobrazení filtrů. Klikněte na ikonu filtru zobrazíte další filtry. Vyberte filtr a klikněte na tlačítko **použít & Spustit** spusťte dotaz s filtrem vybrané.

![Filtrovat protokoly](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrahovat vlastní pole 
V prohledávání protokolu extrahovat [vlastních polí](../platform/custom-fields.md) ze zobrazení seznamu, kde pole nabídky se týká také akce _extrahovat pole z tabulky_.

![Protokolování hledání extrakce polí](media/log-search-transition/extract-fields-log-search.png)

V protokolech monitorování Azure extrahujte vlastních polí ze zobrazení tabulky. Rozbalte položku záznamu kliknutím na šipku na levé straně a klikněte na tlačítko se třemi tečkami pro přístup k _extrahovat pole_ akce.

![Protokoly extrahovat pole](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funkce a skupin počítačů
Uložení hledání v prohledávání protokolu, vyberte **uložená hledání** a **přidat** zadali název, kategorie a text dotazu. Vytvoření [skupinu počítačů](../platform/computer-groups.md) přidáním alias funkce.

![Uložit hledání v protokolu](media/log-search-transition/save-search-log-search.png)

Chcete-li uložit aktuální dotaz protokoly Azure monitoru, vyberte **Uložit**. Změnit **uložit jako** k _– funkce_ a zadejte **Alias funkce** k vytvoření [funkce](functions.md). Vyberte _uložit tento dotaz jako skupinu počítačů_ alias funkce pro použití [skupinu počítačů](../platform/computer-groups.md).

![Uložit dotaz protokolu](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Uložené dotazy
V prohledávání protokolu, jsou k dispozici prostřednictvím položky panelu Akce uložené dotazy **uložená hledání**. V protokolech Azure Monitor, přístup k uložených dotazů z [Průzkumníka dotazů](../log-query/get-started-portal.md#save-queries).

![Průzkumník dotazů](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>K podrobnostem na souhrnné řádků
V prohledávání protokolu můžete kliknutím na řádek v souhrnné dotazu a spusťte další dotaz, který obsahuje podrobné záznamy v daném řádku.

![Přejít k podrobnostem vyhledávání protokolu](media/log-search-transition/drilldown-search.png)

V protokolech Azure Monitor je třeba upravit dotaz, který vrací tyto záznamy. Rozbalte některou řádků ve výsledcích a klikněte **+** vedle hodnoty, které chcete přidat do dotazu. Poté komentář **shrnout** příkazů a spusťte dotaz znovu.

![Přejít k podrobnostem protokoly Azure monitoru](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Provést akci
V prohledávání protokolu můžete [spuštění sady runbook](take-action.md) z výsledku hledání tak, že vyberete **provést akci**.

![Provést akci](media/log-search-transition/take-action-log-search.png)

V protokolech Azure Monitor [vytvořte výstrahu z dotazu protokolu](../platform/alerts-log.md). Skupiny akcí nakonfigurujte jednu nebo více akcí, které se spustí v reakci na upozornění.

![Skupina akcí](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Další postup

- Další informace o novém [protokoly Azure monitoru prostředí](get-started-portal.md).