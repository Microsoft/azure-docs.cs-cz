---
title: Monitorování řešení v Azure Monitor | Microsoft Docs
description: Řešení monitorování v Azure Monitor jsou kolekce pravidel pro logiku, vizualizaci a získávání dat, která poskytují metriky v určité oblasti problému.  Tento článek poskytuje informace o instalaci a používání řešení monitorování.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/23/2020
ms.openlocfilehash: 44094497c98c306b0995f3a74da68809ef66a0ac
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682835"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Monitorování řešení v Azure Monitor

Monitorování řešení využívajících služby v Azure poskytují další přehled o provozu konkrétní aplikace nebo služby. Tento článek poskytuje stručný přehled řešení monitorování v Azure a podrobnosti o jejich používání a instalaci.

> [!NOTE]
> Řešení monitorování se dřív odkazovala na řešení správy.

Řešení monitorování obvykle shromažďují data protokolu a poskytují dotazy a zobrazení k analýze shromážděných dat. Můžou také využít jiné služby, například Azure Automation, k provádění akcí souvisejících s aplikací nebo službou.

Můžete přidat řešení monitorování Azure Monitor pro všechny aplikace a služby, které používáte. Jsou obvykle k dispozici zdarma, ale shromažďují data, která by mohla vyvolat poplatky za využití. Kromě řešení poskytovaných společností Microsoft můžou partneři a zákazníci [vytvářet řešení pro správu](solutions-creating.md) , která se dají používat ve vlastním prostředí nebo zpřístupnit zákazníkům prostřednictvím komunity.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Použití řešení monitorování

Otevřete stránku **Přehled** v Azure monitor pro zobrazení dlaždice pro každé nainstalované řešení v pracovním prostoru. 

1. Přejít na [Azure Portal](https://ms.portal.azure.com). Vyhledejte a vyberte **monitor**.
1. V nabídce **přehledy** vyberte **Další**.
1. Pomocí rozevíracích seznamů v horní části obrazovky změňte pracovní prostor nebo časový rozsah použitý pro dlaždice.
1. Klikněte na dlaždici pro řešení a otevřete jeho zobrazení, které obsahuje podrobnější analýzu shromážděných dat.

![Přehled](media/solutions/overview.png)

Řešení monitorování mohou obsahovat více typů prostředků Azure a můžete si Zobrazit všechny prostředky, které jsou součástí řešení, stejně jako všechny ostatní prostředky. Například všechny dotazy protokolu zahrnuté v řešení jsou uvedeny v části **dotazy řešení** v [Průzkumníku dotazů](../log-query/get-started-portal.md#load-queries) . Tyto dotazy můžete použít při provádění analýzy ad hoc pomocí [dotazů protokolu](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Výpis nainstalovaných řešení monitorování

Pomocí následujícího postupu můžete zobrazit seznam řešení monitorování nainstalovaných ve vašem předplatném.

1. Přejít na [Azure Portal](https://ms.portal.azure.com). Vyhledejte a vyberte **řešení**.
1. Jsou uvedena řešení nainstalovaná ve všech pracovních prostorech. Za názvem řešení následuje název pracovního prostoru, ve kterém je nainstalovaný.
1. Pomocí rozevíracích seznamů v horní části obrazovky můžete filtrovat podle předplatného nebo skupiny prostředků.


![Vypsat všechna řešení](media/solutions/list-solutions-all.png)

Kliknutím na název řešení otevřete jeho stránku Shrnutí. Tato stránka zobrazuje všechna zobrazení zahrnutá v řešení a poskytuje různé možnosti pro samotné řešení a jeho pracovní prostor. Zobrazte stránku souhrnu pro řešení pomocí jednoho z výše uvedených postupů pro výpis řešení a potom klikněte na název řešení.

![Vlastnosti řešení](media/solutions/solution-properties.png)

## <a name="install-a-monitoring-solution"></a>Instalace řešení monitorování

Řešení monitorování od Microsoftu a partnerů jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com). Dostupná řešení můžete vyhledat a nainstalovat pomocí následujícího postupu. Když nainstalujete řešení, musíte vybrat [Log Analytics pracovní prostor](../platform/manage-access.md) , kde se bude řešení instalovat a kde se budou shromažďovat jeho data.

1. V [seznamu řešení pro vaše předplatné](#list-installed-monitoring-solutions)klikněte na **Přidat**.
1. Vyhledejte řešení nebo ho vyhledejte. Řešení můžete také procházet pomocí [tohoto odkazu pro hledání](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Vyhledejte řešení monitorování, které chcete, a přečtěte si jeho popis.
1. Kliknutím na tlačítko **vytvořit** spusťte proces instalace.
1. Po spuštění procesu instalace budete vyzváni k zadání pracovního prostoru Log Analytics a k poskytnutí požadované konfigurace pro řešení.

![Instalace řešení](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalace řešení z komunity

Členové komunity mohou odesílat řešení pro správu do šablon pro rychlý Start Azure. Tato řešení můžete nainstalovat přímo nebo stáhnout šablony pro pozdější instalaci.

1. Pokud chcete propojit pracovní prostor a účet, postupujte podle kroků popsaných v [Log Analytics pracovního prostoru a účtu Automation](#log-analytics-workspace-and-automation-account) .
2. Přejít na [šablony pro rychlý Start Azure](https://azure.microsoft.com/documentation/templates/) 
3. Vyhledejte řešení, které vás zajímá.
4. Výběrem řešení z výsledků zobrazíte jeho podrobnosti.
5. Klikněte na tlačítko **nasadit do Azure** .
6. Budete vyzváni k zadání informací, jako je například skupina prostředků a umístění, spolu s hodnotami pro všechny parametry v řešení.
7. Pro instalaci řešení klikněte na **koupit** .

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics pracovní prostor a účet Automation

Všechna řešení monitorování vyžadují [Log Analytics pracovní prostor](../platform/manage-access.md) k ukládání dat shromažďovaných řešením a k hostování jeho prohledávání a zobrazení protokolů. Některá řešení také vyžadují [účet Automation](../../automation/automation-security-overview.md) , který obsahuje Runbooky a související prostředky. Pracovní prostor a účet musí splňovat následující požadavky.

* Každá instalace řešení může použít jenom jeden Log Analytics pracovní prostor a jeden účet Automation. Řešení můžete nainstalovat samostatně do několika pracovních prostorů.
* Pokud řešení vyžaduje účet Automation, musí být tento pracovní prostor Log Analytics a účet Automation vzájemně propojený. Log Analytics pracovní prostor může být propojený jenom s jedním účtem Automation a účet Automation se dá propojit jenom s jedním pracovním prostorem Log Analytics.
* Aby bylo možné je propojit, musí být pracovní prostor Log Analytics a účet Automation ve stejném předplatném, ale mohou být v různých skupinách prostředků nasazených do stejné oblasti. Výjimkou je pracovní prostor v Východní USA oblasti a účet Automation v Východní USA 2.

Když nainstalujete řešení prostřednictvím Azure Marketplace, budete vyzváni k zadání pracovního prostoru a účtu Automation. Propojení mezi nimi je vytvořeno, pokud již nejsou propojena.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi Log Analyticsm pracovním prostorem a účtem služby Automation

Pomocí následujícího postupu můžete ověřit propojení mezi Log Analytics pracovním prostorem a účtem služby Automation.

1. V Azure Portal vyberte účet Automation.
1. Přejděte do části **související prostředky** v nabídce a vyberte **propojený pracovní prostor**.
1. Pokud je **pracovní prostor** propojený s účtem služby Automation, pak na této stránce je uveden pracovní prostor, se kterým je propojen. Pokud vyberete název pracovního prostoru v seznamu, budete přesměrováni na stránku Přehled pro daný pracovní prostor.

## <a name="remove-a-monitoring-solution"></a>Odebrání řešení monitorování

Pokud chcete nainstalované řešení odebrat, vyhledejte ho v [seznamu nainstalovaných řešení](#list-installed-monitoring-solutions). Kliknutím na název řešení otevřete jeho stránku Souhrn a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

* Získejte [seznam řešení monitorování od Microsoftu](solutions-inventory.md).
* Naučte se [vytvářet dotazy](../log-query/log-query-overview.md) k analýze dat shromažďovaných monitorovacími řešeními.