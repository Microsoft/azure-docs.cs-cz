---
title: Vytváření interaktivních sestav pomocí Azure Monitor sešitů z několika zdrojů dat Azure v dispate | Dokumentace Microsoftu
description: Zjednodušení složitých sestav s předem sestavenými a vlastními parametrizovanými Azure Monitor sešity sestavenými z více zdrojů dat
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: dd5068da31c3aa863fc56022834a28b60ee15004
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166197"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Zdroje dat Azure Monitor sešity

Sešity jsou kompatibilní s velkým počtem zdrojů dat. Tento článek vás provede jednotlivými zdroji dat, které jsou aktuálně k dispozici pro Azure Monitor sešity.

## <a name="logs"></a>Protokoly

Pracovní sešity umožňují dotazování protokolů z následujících zdrojů:

* Protokoly Azure Monitor (Application Insights prostředky a Log Analytics pracovní prostory.)
* Data orientovaných na prostředky (protokoly aktivit)

Autoři sešitu mohou používat dotazy KQL, které transformují podkladová data prostředků k výběru sady výsledků, která se může vizuálně vyznačit jako text, grafy nebo mřížky.

![Snímek obrazovky s rozhraním sestav protokolů v sešitech](./media/workbooks-overview/logs.png)

Autoři sešitu můžou snadno dotazovat se na více zdrojů a vytvořit skutečně sjednocené prostředí pro vytváření sestav.

## <a name="metrics"></a>Metriky

Prostředky Azure emitují [metriky](data-platform-metrics.md) , ke kterým se dá dostat prostřednictvím sešitů. K metrikám lze v sešitech přicházet prostřednictvím specializovaného ovládacího prvku, který umožňuje určit cílové prostředky, požadované metriky a jejich agregaci. Tato data se pak dají vykreslovat v grafech nebo Gridech.

![Snímek obrazovky s metrikami v grafech metriky využití procesoru](./media/workbooks-overview/metrics-graph.png)

![Snímek obrazovky rozhraní metriky sešitu](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Graf prostředků Azure 

Pracovní sešity podporují dotazování na prostředky a jejich metadata pomocí Azure Resource graphu (ARG). Tato funkce se primárně používá k vytváření vlastních oborů dotazů pro sestavy. Rozsah prostředků je vyjádřen prostřednictvím KQL podmnožiny, kterou ARG podporuje – což je často dostačující pro běžné případy použití.

Chcete-li, aby ovládací prvek dotazu použil tento zdroj dat, použijte rozevírací seznam typ dotazu pro výběr grafu prostředků Azure a vyberte předplatná, která chcete cílit. Pomocí ovládacího prvku pro dotaz přidejte ARG KQL-submnožiny, které vyberou zajímavou podmnožinu prostředků.


![Snímek obrazovky s dotazem na KQL v grafu prostředků Azure](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Výstrahy (Preview)

Pracovní sešity umožňují uživatelům vizualizovat aktivní výstrahy související s jejich prostředky. Tato funkce umožňuje vytváření sestav, které společně přinášejí data oznámení (výstrahy) a diagnostické informace (metriky, protokoly) do jedné sestavy. Tyto informace se dají taky spojit dohromady a vytvářet tak bohatě sestavování, které kombinuje přehledy napříč těmito zdroji dat.

Chcete-li, aby ovládací prvek dotazu použil tento zdroj dat, použijte rozevírací seznam typ dotazu a zvolte možnost výstrahy a vyberte předplatná, skupiny prostředků nebo prostředky, které chcete cílit. Pomocí rozevíracích seznamu filtru výstrah můžete vybrat zajímavou podmnožinu výstrah pro potřeby analýzy.

![Snímek obrazovky s dotazem výstrahy](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Stav úloh (Preview)

Azure Monitor mají funkce, které aktivně monitorují dostupnost a výkon hostovaných operačních systémů Windows nebo Linux. Azure Monitor klíčové komponenty modelů a jejich vztahy, kritéria, jak změřit stav těchto komponent a které komponenty vás upozorní, když se zjistí stav není v pořádku. Pracovní sešity umožňují uživatelům používat tyto informace k vytváření propracovaných interaktivních sestav.

Pokud chcete, aby ovládací prvek dotazu použil tento zdroj dat, použijte rozevírací seznam **typ dotazu** k výběru možnosti stav úloh a vyberte předplatné, skupinu prostředků nebo prostředky virtuálního počítače k cíli. Rozevírací seznam filtru stavu použijte k výběru zajímavé podmnožiny stavových incidentů pro potřeby analýzy.

![Snímek obrazovky s dotazem výstrahy](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Stav prostředku Azure 

Pracovní sešity podporují získání stavu prostředků Azure a jejich kombinaci s jinými zdroji dat a vytvářejí bohatou a interaktivní sestavy o stavu.

Chcete-li, aby ovládací prvek dotazu použil tento zdroj dat, použijte rozevírací seznam **typ dotazu** k výběru možnosti stav služby Azure a vyberte prostředky, které chcete cílit. Pomocí rozevíracího seznamu filtru stavu můžete vybrat zajímavou podmnožinu problémů s prostředky pro potřeby analýzy.

![Snímek obrazovky s dotazem výstrahy](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Průzkumník dat Azure (Preview)

Sešity teď podporují dotazování z clusterů [Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/) pomocí výkonného dotazovacího jazyka [Kusto](https://docs.microsoft.com/azure/kusto/query/index) .   

![Snímek obrazovky s oknem dotazu Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Další kroky

* [Začínáme](workbooks-visualizations.md) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.
