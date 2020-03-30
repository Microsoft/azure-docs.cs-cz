---
title: Zobrazení a analýza dat v Azure Log Analytics | Dokumenty společnosti Microsoft
description: Pomoc pro uživatele protokolu Log Analytics vyhledávání protokolu azure monitor u dotazu na protokol.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670130"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Přechod z hledání protokolu Log Analytics do protokolů Azure Monitor
Hledání protokolu v Log Analytics byl nedávno nahrazen nové prostředí pro analýzu protokolů Azure Monitor. Stránka hledání protokolu je momentálně přístupná prostřednictvím položky nabídky **Protokoly (klasické)** na stránce **pracovních prostorů Analýzy protokolů** na webu Azure Portal, ale bude odebrána 15. Tento článek popisuje rozdíly mezi dvěma prostředími, které vám pomohou přejít z hledání protokolu. 

## <a name="filter-results-of-a-query"></a>Filtrování výsledků dotazu
Při hledání protokolu se při doručení výsledků hledání zobrazí seznam filtrů. Vyberte filtr a kliknutím na **Použít** spusťte dotaz s vybraným filtrem.

![Filtr hledání protokolu](media/log-search-transition/filter-log-search.png)

V protokolech Azure Monitoru vyberte *Filtr (náhled)* pro zobrazení filtrů. Kliknutím na ikonu filtru zobrazíte filtry sčítání. Vyberte filtr a kliknutím na **Použít & Spustit** spusťte dotaz s vybraným filtrem.

![Protokoly, filtr](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrahovat vlastní pole 
Při hledání protokolu extrahnete [vlastní pole](../platform/custom-fields.md) ze zobrazení seznam, kde nabídka pole obsahuje pole _Akce Extrahovat z tabulky_.

![Pole extrakce hledání protokolu](media/log-search-transition/extract-fields-log-search.png)

V protokolech Azure Monitor extrahovat vlastní pole ze zobrazení tabulky. Rozbalte záznam kliknutím na šipku vlevo a kliknutím na tři tečky získáte akci _Extrahovat pole._

![Protokoly extrahovat pole](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funkce a počítačové skupiny
Chcete-li uložit hledání do hledání protokolu, vyberte **Uložená hledání** a **Přidat,** abyste zadali název, kategorii a text dotazu. Přidáním aliasu funkce vytvořte [skupinu počítačů.](../platform/computer-groups.md)

![Uložit hledání protokolu](media/log-search-transition/save-search-log-search.png)

Pokud chcete aktuální dotaz uložit do protokolů Azure Monitor, vyberte **Uložit**. Změňte **funkci Uložit jako** _funkci_ a zadejte **alias funkce** pro vytvoření [funkce](functions.md). Chcete-li použít alias funkce pro [skupinu počítačů](../platform/computer-groups.md), vyberte možnost _Uložit tento dotaz jako skupinu počítačů_ .

![Uložit dotaz protokolu](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Uložené dotazy
Ve vyhledávání protokolů jsou uložené dotazy k dispozici prostřednictvím položky panelu akcí **Uložená hledání**. V protokolech Azure Monitor uvázni o uložené dotazy z [Průzkumníka dotazů](../log-query/get-started-portal.md#save-queries).

![Průzkumník dotazů](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Přechod k podrobnostem na souhrnných řádcích
Ve vyhledávání protokolů můžete kliknout na řádek souhrnného dotazu a spustit další dotaz, který obsahuje podrobné záznamy v tomto řádku.

![Podrobnosti hledání protokolu](media/log-search-transition/drilldown-search.png)

V protokolech Azure Monitor, musíte upravit dotaz vrátit tyto záznamy. Rozbalte jeden z řádků ve **+** výsledcích a klikněte na další hodnotu, kterou chcete přidat do dotazu. Potom zakomentujte příkaz **summarize** a spusťte dotaz znovu.

![Procházení protokolů azure monitoru](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Přijmout opatření
Ve vyhledávání protokolů můžete [spustit runbook](take-action.md) z výsledku hledání výběrem **akce Take**.

![Přijmout opatření](media/log-search-transition/take-action-log-search.png)

V protokolech Azure Monitor [vytvořte výstrahu z dotazu protokolu](../platform/alerts-log.md). Nakonfigurujte skupinu akcí s jednou nebo více akcemi, které budou spuštěny v reakci na výstrahu.

![Skupina akcí](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Další kroky

- Další informace o novém [prostředí protokolů Azure Monitoru](get-started-portal.md).
