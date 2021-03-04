---
title: Azure Monitor aktualizace terminologie | Microsoft Docs
description: Popisuje nedávné změny v terminologii provedené v Azure Monitoring Services.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: fea85a85db1e555adea63f5b65b09a9285d714da
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031969"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor změny názvů a terminologie
V nedávné době byly provedeny významné změny, které se Azure Monitor v poslední době s různými službami konsolidovány, aby se zjednodušilo monitorování pro zákazníky Azure. Tento článek popisuje nedávné změny názvů a terminologie v dokumentaci Azure Monitor.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Říjen 2019 – diagnostický protokol do protokolu prostředků
Diagnostické protokoly se změnily na "protokoly prostředků", aby lépe odpovídaly, co se skutečně shromažďují. Pojem "nastavení diagnostiky" zůstává stejný.  

## <a name="february-2019---log-analytics-terminology"></a>Února 2019 – Log Analytics terminologie
Po konsolidaci různých služeb v rámci Azure Monitor provedeme další krok úpravou terminologie v naší dokumentaci, aby lépe popsali Azure Monitor službu a její různé komponenty. 

### <a name="log-analytics"></a>Log Analytics
Data protokolu Azure Monitor jsou stále uložená v pracovním prostoru Log Analytics a stále se shromažďují a analyzují pomocí stejné Log Analytics služby, ale měníme tento termín _Log Analytics_ na mnoha místech až po _Azure monitor protokolů_. Tento termín lépe odráží svou roli v Azure Monitor a poskytuje lepší konzistenci se [metrikami v Azure monitor](essentials/data-platform-metrics.md).

Pojem _Log Analytics_ teď platí hlavně pro stránku v Azure Portal použitou k zápisu a spouštění dotazů a k analýze dat protokolu. Je to funkční ekvivalent [Průzkumníka metrik](essentials/metrics-charts.md), což je stránka v Azure Portal používaná k analýze dat metrik.

### <a name="log-analytics-workspaces"></a>Pracovní prostory služby Log Analytics
[Pracovní prostory](logs/manage-access.md) , které uchovávají data protokolu v Azure monitor jsou stále označovány jako Log Analytics pracovní prostory. Nabídka **Log Analytics** v Azure Portal byla přejmenována na **Log Analytics pracovní prostory** a je tam, kde [vytvoříte nové pracovní prostory](logs/quick-create-workspace.md) a nakonfigurujete zdroje dat. Analyzujte protokoly a další data monitorování v **Azure monitor** a nakonfigurujte svůj pracovní prostor v **pracovních prostorech Log Analytics**.

### <a name="management-solutions"></a>Řešení pro správu
[Řešení pro správu](insights/solutions.md) byla přejmenována na _řešení monitorování_, což lépe popisuje jejich funkce.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>2018. srpna – konsolidace monitorovacích služeb do Azure Monitor
Log Analytics a Application Insights byly konsolidovány do Azure Monitor za účelem poskytování jediného integrovaného prostředí pro monitorování prostředků Azure a hybridních prostředí. Z těchto služeb se neodebraly žádné funkce a uživatelé můžou provádět stejné scénáře, jako kdyby byly vždycky dokončené bez ztráty nebo ohrožení jakýchkoli funkcí.

Dokumentace pro každou z těchto služeb byla konsolidována do jedné sady obsahu pro Azure Monitor. To vám pomůže čtenářům při hledání celého obsahu pro konkrétní scénář monitorování v jednom umístění, a to na rozdíl od toho, že je nutné odkazovat na více sad obsahu. Vzhledem k toho, že konsolidovaná služba se vyvíjí, obsah se bude lépe integrovat.

Další funkce, které byly považovány za součást Log Analytics jako jsou například agenti a zobrazení, byly také přemístěné jako funkce Azure Monitor. Jejich funkce se nezměnila na jiné než potenciální vylepšení jejich prostředí v Azure Portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Duben 2018 – vyřazení značky Operations Management Suite
Operations Management Suite (OMS) je sdružením následujících služeb správy Azure pro účely licencování:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Pro tyto služby jsme zavedli nové ceny](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)a sdružování OMS už pro nové zákazníky není dostupné. Žádná ze služeb, které byly součástí OMS, se nezměnily, s výjimkou konsolidace do Azure Monitor popsané výše. 




## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled Azure monitor](overview.md) , který popisuje jeho různé komponenty a funkce.
- Přečtěte si o [přechodu portálu OMS](./logs/oms-portal-transition.md).
