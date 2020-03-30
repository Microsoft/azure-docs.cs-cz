---
title: Aktualizace terminologie Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje nedávné změny terminologie provedené ve službách monitorování Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0c5e1096b0780d2f9e50389f772e63344935012b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274135"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Změny názvů a terminologie Azure Monitoru
V azure monitoru byly nedávno provedeny významné změny, kdy se konsolidují různé služby, aby se zjednodušilo monitorování pro zákazníky Azure. Tento článek popisuje nedávné změny názvu a terminologie v dokumentaci azure monitoru.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Říjen 2019 – diagnostický protokol do protokolu zdrojů
"Diagnostické protokoly" byly změněny na "protokoly prostředků" tak, aby lépe odpovídaly tomu, co je skutečně shromažďováno. Termín "diagnostické nastavení" zůstává stejný.  

## <a name="february-2019---log-analytics-terminology"></a>Únor 2019 - Terminologie log analytics
Po konsolidaci různých služeb v rámci Azure Monitoru děláme další krok úpravou terminologie v naší dokumentaci, abychom lépe popsali službu Azure Monitor a její různé součásti. 

### <a name="log-analytics"></a>Log Analytics
Data protokolu Azure Monitor se stále ukládají v pracovním prostoru Log Analytics a stále se shromažďují a analyzují stejnou službou Log Analytics, ale měníme termín _Log Analytics_ na mnoha místech na _protokoly Azure Monitor_. Tento termín lépe odráží jeho roli ve službě Azure Monitor a poskytuje lepší konzistenci s [metrikami v Azure Monitoru](platform/data-platform-metrics.md).

Analýza _protokolu termínů_ se teď primárně vztahuje na stránku na portálu Azure, která se používá k psaní a spouštění dotazů a analýze dat protokolu. Je to funkční ekvivalent [průzkumníka metrik](platform/metrics-charts.md), což je stránka na webu Azure Portal používaná k analýze dat metriky.

### <a name="log-analytics-workspaces"></a>Pracovní prostory služby Log Analytics
[Pracovní prostory,](platform/manage-access.md) které uchovávají data protokolu v Azure Monitoru, se pořád označují jako pracovní prostory Analýzy protokolů. Nabídka **Log Analytics** na portálu Azure byla přejmenována na **pracovní prostory Log Analytics** a je místo, kde [můžete vytvářet nové pracovní prostory](learn/quick-create-workspace.md) a konfigurovat zdroje dat. Analyzujte protokoly a další data monitorování v **Azure Monitoru** a nakonfigurujte pracovní prostor v **pracovních prostorech Log Analytics**.

### <a name="management-solutions"></a>Řešení pro správu
[Řešení pro správu](insights/solutions.md) byla přejmenována na _řešení monitorování_, která lépe popisují jejich funkčnost.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Srpen 2018 – konsolidace monitorovacích služeb do Azure Monitoru
Log Analytics a Application Insights byly konsolidovány do Azure Monitoru, aby poskytovaly jediné integrované prostředí pro monitorování prostředků Azure a hybridních prostředí. Z těchto služeb nebyly odebrány žádné funkce a uživatelé mohou provádět stejné scénáře, které vždy dokončili, bez ztráty nebo ohrožení zabezpečení jakýchkoli funkcí.

Dokumentace pro každou z těchto služeb byla sloučena do jedné sady obsahu pro Azure Monitor. To pomůže čtenáři při hledání veškerého obsahu pro konkrétní scénář monitorování v jednom umístění, na rozdíl od nutnosti odkazovat na více sad obsahu. Jak se konsolidovaná služba vyvíjí, obsah se bude více integrovat.

Další funkce, které byly považovány za součást analýzy protokolů, jako jsou agenti a zobrazení, byly také přemístěny jako funkce Azure Monitoru. Jejich funkce se nezměnila, kromě potenciálních vylepšení jejich prostředí na webu Azure Portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Duben 2018 - Odchod značky Operations Management Suite
Operations Management Suite (OMS) bylo sdružování následujících služeb správy Azure pro účely licencování:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Pro tyto služby byly zavedeny nové ceny](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)a sdružování OMS již není pro nové zákazníky k dispozici. Žádná ze služeb, které byly součástí OMS se změnily, s výjimkou konsolidace do Azure Monitor popsané výše. 




## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled Azure Monitoru,](overview.md) který popisuje jeho různé součásti a funkce.
- Seznamte se s [přechodem portálu OMS](../log-analytics/log-analytics-oms-portal-transition.md).
