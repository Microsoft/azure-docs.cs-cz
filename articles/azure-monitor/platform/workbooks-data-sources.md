---
title: Zdroje dat sešitů Azure Monitor | Dokumenty Microsoftu
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů Azure Monitor vytvořených z více zdrojů dat
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248577"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Zdroje dat sešitů Azure Monitor

Sešity jsou kompatibilní s velkým počtem zdrojů dat. Tento článek vás provede zdroje dat, které jsou aktuálně k dispozici pro sešity Azure Monitor.

## <a name="logs"></a>Protokoly

Sešity umožňují dotazování protokolů z následujících zdrojů:

* Protokoly monitorování Azure (prostředky přehledů aplikací a pracovní prostory analýzy protokolů.)
* Data zaměřená na zdroje (protokoly aktivit)

Autoři sešitu mohou pomocí dotazů KQL, které transformují podkladová data prostředků, vybrat sadu výsledků, která může být vizualizována jako text, grafy nebo mřížky.

![Snímek obrazovky s rozhraním sestavy protokolů sešitů](./media/workbooks-overview/logs.png)

Autoři sešitu se mohou snadno dotazovat na více prostředků a vytvářet tak skutečně jednotné bohaté prostředí pro vytváření sestav.

## <a name="metrics"></a>Metriky

Prostředky Azure [vyzařují metriky,](data-platform-metrics.md) ke kterým se dá přistupovat prostřednictvím sešitů. Metriky lze přistupovat v sešitech prostřednictvím specializovaného ovládacího prvku, který umožňuje určit cílové prostředky, požadované metriky a jejich agregace. Tato data pak mohou být vykreslena v grafech nebo mřížkách.

![Snímek obrazovky s grafy metrik sešitu s využitím procesoru](./media/workbooks-overview/metrics-graph.png)

![Snímek obrazovky s rozhraním metrik sešitu](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Sešity podporují dotazování na prostředky a jejich metadata pomocí Azure Resource Graph (ARG). Tato funkce se používá především k vytvoření vlastní obory dotazu pro sestavy. Obor prostředků je vyjádřena prostřednictvím podmnožiny KQL, které podporuje ARG – což je často dostačující pro běžné případy použití.

Chcete-li ovládací prvek dotazu použít tento zdroj dat, použijte rozbalovací seznam Typ dotazu k výběru Azure Resource Graph a vyberte předplatná, na které chcete cílit. Pomocí ovládacího prvku Dotaz přidejte podmnožinu ARG KQL, která vybere zajímavou podmnožinu prostředků.


![Snímek obrazovky s dotazem KQL grafu prostředků Azure](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Upozornění (náhled)

Sešity umožňují uživatelům vizualizovat aktivní výstrahy související s jejich prostředky. Tato funkce umožňuje vytváření sestav, které sdružují data oznámení (výstraha) a diagnostické informace (metriky, protokoly) do jedné sestavy. Tyto informace lze také spojit a vytvořit tak bohaté sestavy, které kombinují přehledy napříč těmito zdroji dat.

Chcete-li, aby ovládací prvek dotazu používal tento zdroj dat, zvolte pomocí rozevíracího přehledu Typ dotazu výstrahy a vyberte předplatná, skupiny prostředků nebo prostředky, na které chcete cílit. Pomocí rozevíracích seznamů filtru výstrah vyberte zajímavou podmnožinu výstrah pro vaše analytické potřeby.

![Snímek obrazovky s dotazem na výstrahy](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Stav pracovního vytížení (náhled)

Azure Monitor má funkce, které proaktivně monitorují dostupnost a výkon hostovacích operačních systémů Windows nebo Linux. Azure Monitor modely klíčové součásti a jejich vztahy, kritéria pro měření stavu těchto součástí a které součásti vás upozorní, když je zjištěn a stavu není v pořádku. Sešity umožňují uživatelům používat tyto informace k vytváření rozšířených interaktivních sestav.

Chcete-li, aby ovládací prvek dotazu používal tento zdroj dat, zvolte pomocí rozevíracího **přehledu Typ dotazu** stav pracovního vytížení a vyberte předplatné, skupinu prostředků nebo prostředky virtuálních aplikací, na které chcete cílit. Pomocí rozevíracích seznamů filtru stavu vyberte zajímavou podmnožinu zdravotních incidentů pro vaše analytické potřeby.

![Snímek obrazovky s dotazem na výstrahy](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Stav prostředků Azure 

Sešity podporují získání stavu prostředků Azure a jeho kombinaci s jinými zdroji dat za účelem vytváření bohatých interaktivních sestav stavu

Chcete-li ovládací prvek dotazu použít tento zdroj dat, použijte rozbalovací nabídka **Typ dotazu** k výběru stavu Azure a vyberte prostředky, na které chcete cílit. Pomocí rozevíracích seznamů filtru stavu vyberte zajímavou podmnožinu problémů se zdroji pro vaše analytické potřeby.

![Snímek obrazovky s dotazem na výstrahy](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (preview)

Sešity teď mají podporu pro dotazování z clusterů [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) s výkonným dotazovacím jazykem [Kusto.](https://docs.microsoft.com/azure/kusto/query/index)   

![Snímek obrazovky okna dotazu Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Další kroky

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
