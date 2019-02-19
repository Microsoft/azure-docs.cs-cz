---
title: Azure Monitor rebranding | Dokumentace Microsoftu
description: Popisuje poslední brandingu a název změny nedávno provedené služeb Azure pro správu.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 874c0eca97ba7fea7543e41c75e642108141047f
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408489"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor pojmenování a terminologie změny
Významné byly provedeny změny do Azure monitoru nedávno s různými službami konsolidaci zjednodušit sledování pro zákazníky Azure. Tento článek popisuje poslední název a terminologie změnami dokumentace ke službě Azure Monitor.

## <a name="february-2019---log-analytics-terminology"></a>. Února 2019 - terminologie Log Analytics
Po sloučení různé služby Azure Monitor přesměrujeme na další krok úpravou terminologii používané v naší dokumentaci zájmu lepšího popsání služby Azure Monitor a jeho různé součásti. 

### <a name="log-analytics"></a>Log Analytics
Data jsou stále protokolů Azure Monitor uložené v pracovním prostoru Log Analytics je stále shromažďují a analyzována pomocí stejné služby Log Analytics, ale měníme termín _Log Analytics_ na mnoha místech na _protokoly Azure monitoru_ . Tento termín lépe odpovídají jeho role ve službě Azure Monitor a poskytuje lepší konzistence [metriky ve službě Azure Monitor](platform/data-collection.md).

Termín _protokolu analytics_ nyní především se vztahuje na stránce na webu Azure Portal použít k zápisu a spouštět dotazy a analyzovat data protokolů. Je ekvivalentní [Průzkumníka metrik](platform/metrics-charts.md), což je stránka na webu Azure Portal slouží k analýze data metriky.

### <a name="log-analytics-workspaces"></a>Pracovní prostory Log Analytics
[Pracovní prostory](platform/manage-access.md) , který obsahovat data protokolu ve službě Azure Monitor se stále označují jako pracovní prostory Log Analytics. **Log Analytics** nabídky na webu Azure Portal se přejmenoval na **pracovních prostorů Log Analytics** a je tam, kde jste [vytvořit nové pracovní prostory](learn/quick-create-workspace.md) a nakonfigurovat datové zdroje. Analýza protokolů a dalšími daty monitorování v **Azure Monitor** a konfigurujte váš pracovní prostor v **pracovních prostorů Log Analytics**.

### <a name="management-solutions"></a>Řešení pro správu
[Řešení pro správu](insights/solutions.md) byl přejmenován na _řešení monitorování_, lépe vystihuje jejich funkce.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>. Srpna 2018 - konsolidovaná sledování služeb do služby Azure Monitor
Log Analytics a Application Insights se zkonsolidovaly do služby Azure Monitor k poskytnutí jedné integrované prostředí pro monitorování prostředků Azure a hybridní prostředí. Žádné funkce se odebrala z těchto služeb a uživatelé mohou provádět stejné scénáře, které jste vždy dokončeny bez ztráty nebo ohrožení jakékoli další funkce.

Dokumentace ke službě pro každou z těchto služeb se začlenil do jediné sady obsahu pro monitorování Azure. To vám pomůže čtečky při nalezení veškerý obsah pro konkrétní scénář monitorování v jednom umístění – na rozdíl od nutnosti odkazovat více sady obsahu. Jak službu konsolidované vyvíjí, obsah se více integrují.

Další funkce, které byly považováno za součást Log Analytics, jako jsou agenti a zobrazení mají nebyl umístěn také jako funkce služby Azure Monitor. Jejich funkce nezměnil, jiné než potenciálních zlepšení jejich prostředí na webu Azure Portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Platný od dubna 2018 – značky vyřazení z Operations Management Suite
Operations Management Suite (OMS) je sdružení následující služeb Azure pro správu pro účely licencování:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Nový cenový model byl zaveden pro tyto služby](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), a sdružování OMS už nejsou k dispozici pro nové zákazníky. Žádná ze služeb, které byly součástí OMS jste změnili, s výjimkou konsolidace do služby Azure Monitor je popsáno výše. 




## <a name="next-steps"></a>Další postup

- Přečtěte si [Přehled služby Azure Monitor](overview.md) , který popisuje jeho různé součásti a funkce.
- Další informace o [přechod na portálu OMS](../log-analytics/log-analytics-oms-portal-transition.md).