---
title: Monitorování řešení v Azure Monitor | Microsoft Docs
description: Řešení monitorování v Azure Monitor jsou kolekce pravidel pro logiku, vizualizaci a získávání dat, která poskytují metriky v určité oblasti problému.  Tento článek poskytuje informace o instalaci a používání řešení monitorování.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275201"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Monitorování řešení v Azure Monitor
Monitorování řešení využívajících služby v Azure poskytují další přehled o provozu konkrétní aplikace nebo služby. Tento článek poskytuje stručný přehled řešení monitorování v Azure a podrobnosti o jejich používání a instalaci.

> [!NOTE]
> Řešení monitorování se dřív odkazovala na řešení správy.

Řešení monitorování obvykle shromažďují data protokolu a poskytují dotazy a zobrazení k analýze shromážděných dat. Může využít i jiné služby, jako je Azure Automation k provedení akce související s aplikací nebo službou.

Můžete přidat řešení monitorování Azure Monitor pro všechny aplikace a služby, které používáte. Jsou obvykle k dispozici zdarma, ale shromažďují data, která by mohla vyvolat poplatky za využití. Kromě řešení poskytovaných společností Microsoft můžou partneři a zákazníci [vytvářet řešení pro správu](solutions-creating.md) , která se dají používat ve vlastním prostředí nebo zpřístupnit zákazníkům prostřednictvím komunity.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Použití řešení monitorování
Otevřete stránku **Přehled** v Azure monitor pro zobrazení dlaždice pro každé nainstalované řešení v pracovním prostoru. 

1. Přejděte na [Azure Portal](https://ms.portal.azure.com). Vyhledejte a vyberte **monitor**.
1. V nabídce **přehledy** vyberte **Další**.
1. Pomocí pole rozevíracího seznamu v horní části obrazovky můžete změnit pracovní prostor nebo časový rozsah pro dlaždice.
1. Klikněte na dlaždici pro řešení a otevřete jeho zobrazení, které obsahuje podrobnější analýzu shromážděných dat.

![Přehled](media/solutions/overview.png)

Řešení monitorování mohou obsahovat více typů prostředků Azure a můžete si Zobrazit všechny prostředky, které jsou součástí řešení, stejně jako všechny ostatní prostředky. Například všechny dotazy protokolu zahrnuté v řešení jsou uvedeny v části **dotazy řešení** v [Průzkumníku dotazů](../log-query/get-started-portal.md#load-queries) . Tyto dotazy můžete použít při provádění analýzy ad hoc pomocí [dotazů protokolu](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Výpis nainstalovaných řešení monitorování 
Pomocí následujícího postupu můžete zobrazit seznam řešení monitorování nainstalovaných ve vašem předplatném.

1. Přejděte na [Azure Portal](https://ms.portal.azure.com). Vyhledejte a vyberte **řešení**.
1. Řešení, které jsou nainstalovány ve všech pracovních prostorů jsou uvedeny. Za názvem řešení následuje název pracovního prostoru, ve kterém je nainstalovaný.
1. Pomocí pole rozevíracího seznamu v horní části obrazovky můžete filtrovat podle předplatného nebo skupiny prostředků.


![Seznam všech řešení](media/solutions/list-solutions-all.png)

Klikněte na název řešení, otevřete její souhrnnou stránku. Tato stránka zobrazuje všechna zobrazení zahrnutý v řešení a nabízí různé možnosti pro řešení samostatně a její pracovní prostor. Zobrazit na souhrnné stránce řešení pomocí jednoho z postupů nad do seznamu řešení a potom klikněte na název řešení.

![Vlastnosti řešení](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Instalace řešení monitorování
Řešení monitorování od Microsoftu a partnerů jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com). Můžete vyhledat dostupná řešení a nainstalujte je pomocí následujícího postupu. Když nainstalujete řešení, musíte vybrat [Log Analytics pracovní prostor](../platform/manage-access.md) , kde se bude řešení instalovat a kde se budou shromažďovat jeho data.

1. V [seznamu řešení pro vaše předplatné](#list-installed-monitoring-solutions)klikněte na **Přidat**.
1. Vyhledejte řešení nebo ho vyhledejte. Řešení můžete také procházet pomocí [tohoto odkazu pro hledání](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Vyhledejte řešení monitorování, které chcete, a přečtěte si jeho popis.
1. Kliknutím na tlačítko **vytvořit** spusťte proces instalace.
1. Po spuštění procesu instalace budete vyzváni k zadání pracovního prostoru Log Analytics a k poskytnutí požadované konfigurace pro řešení.

![Instalace řešení](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalace řešení od komunity
Členové komunity můžete odeslat řešení pro správu šablon rychlý start Azure. Můžete nainstalovat přímo na tato řešení nebo si je stáhnout šablony pro pozdější instalaci.

1. Pokud chcete propojit pracovní prostor a účet, postupujte podle kroků popsaných v [Log Analytics pracovního prostoru a účtu Automation](#log-analytics-workspace-and-automation-account) .
2. Přejít na [šablony pro rychlý Start Azure](https://azure.microsoft.com/documentation/templates/) 
3. Vyhledejte řešení, které vás zajímají.
4. Vyberte řešení ve výsledcích zobrazíte její podrobnosti.
5. Klikněte na tlačítko **nasadit do Azure** .
6. Budete vyzváni k zadání informací o například skupinu prostředků a umístění kromě hodnot pro všechny parametry v řešení.
7. Pro instalaci řešení klikněte na **koupit** .


## <a name="log-analytics-workspace-and-automation-account"></a>Pracovní prostor log Analytics a účet služby Automation
Všechna řešení monitorování vyžadují [Log Analytics pracovní prostor](../platform/manage-access.md) k ukládání dat shromažďovaných řešením a k hostování jeho prohledávání a zobrazení protokolů. Některá řešení také vyžadují [účet Automation](../../automation/automation-security-overview.md#automation-account-overview) , který obsahuje Runbooky a související prostředky. Pracovní prostor a účet, musí splňovat následující požadavky.

* Každou instalaci řešení lze použít pouze jeden pracovní prostor Log Analytics a jeden účet služby Automation. Řešení můžete nainstalovat samostatně do několika pracovních prostorů.
* Pokud řešení vyžaduje účet Automation, pak pracovní prostor Log Analytics a účet Automation musí být vzájemně propojený. Pracovní prostor Log Analytics může propojit jen do jednoho účtu Automation a účet Automation, které můžou být propojené jenom k jednomu pracovnímu prostoru Log Analytics.
* Propojení, pracovní prostor Log Analytics a účet Automation musí být ve stejné skupině prostředků a oblasti. Výjimkou je pracovní prostor v oblasti USA – východ a účet Automation v oblasti východní USA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Vytvořte propojení mezi pracovní prostor Log Analytics a účet služby Automation
Jak určit pracovní prostor Log Analytics a účet Automation závisí na metodě instalace pro vaše řešení.

* Když nainstalujete řešení prostřednictvím Azure Marketplace, budete vyzváni k zadání pracovního prostoru a účtu Automation. Pokud již nejsou propojeny se vytvoří propojení mezi nimi.
* Pro řešení mimo Azure Marketplace je třeba propojit pracovní prostor Log Analytics a účet Automation, před instalací řešení. Provedete to výběrem libovolné řešení na webu Azure Marketplace a vyberete pracovní prostor Log Analytics a účet Automation. Není nutné skutečně nainstalovat řešení, protože odkaz je vytvořen poté, co jsou vybrané pracovní prostor Log Analytics a účet Automation. Po vytvoření propojení tohoto pracovního prostoru Log Analytics a účet Automation, můžete použít pro každé řešení.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi pracovní prostor Log Analytics a účet služby Automation
Propojení pracovního prostoru Log Analytics a účet Automation pomocí následujícího postupu můžete ověřit.

1. Vyberte účet Automation na webu Azure Portal.
1. Přejděte do části **související prostředky** v nabídce.
1. Pokud je nastavení **pracovní prostor** povolené, tento účet se propojí s pracovním prostorem Log Analytics. Kliknutím na **pracovní prostor** můžete zobrazit podrobnosti pracovního prostoru.

## <a name="remove-a-monitoring-solution"></a>Odebrání řešení monitorování
Pokud chcete nainstalované řešení odebrat, vyhledejte ho v [seznamu nainstalovaných řešení](#list-installed-monitoring-solutions). Kliknutím na název řešení otevřete jeho stránku Souhrn a pak klikněte na **Odstranit**.


## <a name="next-steps"></a>Další kroky
* Získejte [seznam řešení monitorování od Microsoftu](solutions-inventory.md).
* Naučte se [vytvářet dotazy](../log-query/log-query-overview.md) k analýze dat shromažďovaných monitorovacími řešeními.

