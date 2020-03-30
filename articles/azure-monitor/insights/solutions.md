---
title: Řešení monitorování ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Řešení monitorování ve službě Azure Monitor jsou kolekce pravidel logiky, vizualizace a získávání dat, která poskytují metriky otočné kolem konkrétní problémové oblasti.  Tento článek obsahuje informace o instalaci a používání řešení monitorování.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275201"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Řešení monitorování ve službě Azure Monitor
Monitorování řešení využívají služby v Azure k poskytnutí dalších informací o provozu konkrétní aplikace nebo služby. Tento článek obsahuje stručný přehled řešení monitorování v Azure a podrobnosti o jejich použití a instalaci.

> [!NOTE]
> Monitorovací řešení byla dříve označována jako řešení pro správu.

Řešení monitorování obvykle shromažďují data protokolu a poskytují dotazy a zobrazení pro analýzu shromážděných dat. Mohou také využít jiné služby, jako je Azure Automation k provádění akcí souvisejících s aplikací nebo službou.

Do Azure Monitoru můžete přidat řešení monitorování pro všechny aplikace a služby, které používáte. Obvykle jsou k dispozici bez nákladů, ale shromažďují data, která by mohla vyvolat poplatky za využití. Kromě řešení poskytovaných společností Microsoft mohou partneři a zákazníci [vytvářet řešení pro správu,](solutions-creating.md) která budou použita v jejich vlastním prostředí nebo zpřístupněna zákazníkům prostřednictvím komunity.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Použití řešení monitorování
Otevřete stránku **Přehled** v Azure Monitoru a zobrazte dlaždici pro každé řešení nainstalované v pracovním prostoru. 

1. Přejděte na [portál Azure](https://ms.portal.azure.com). Vyhledejte a vyberte **možnost Sledovat**.
1. V nabídce **Přehledy** vyberte **Další**.
1. Pomocí rozevíracích polí v horní části obrazovky můžete změnit pracovní plochu nebo časový rozsah použitý pro dlaždice.
1. Kliknutím na dlaždici otevřete její pohled, který obsahuje podrobnější analýzu shromážděných dat.

![Přehled](media/solutions/overview.png)

Řešení monitorování mohou obsahovat více typů prostředků Azure a můžete zobrazit všechny prostředky, které jsou součástí řešení, stejně jako jakýkoli jiný prostředek. Například všechny dotazy protokolu zahrnuté v řešení jsou **uvedeny** v části Dotazy řešení v [průzkumníku dotazu:](../log-query/get-started-portal.md#load-queries) Tyto dotazy můžete použít při provádění analýzy ad hoc s [dotazy protokolu](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Seznam nainstalovaných monitorovacích řešení 
Pomocí následujícího postupu můžete zobrazit seznam řešení monitorování nainstalovaných v předplatném.

1. Přejděte na [portál Azure](https://ms.portal.azure.com). Vyhledejte a vyberte **řešení**.
1. Jsou uvedena řešení nainstalovaná ve všech pracovních prostorech. Za názvem řešení následuje název pracovního prostoru, ve kterém je nainstalován.
1. Pomocí rozevíracích polí v horní části obrazovky můžete filtrovat podle předplatného nebo skupiny prostředků.


![Seznam všech řešení](media/solutions/list-solutions-all.png)

Kliknutím na název řešení otevřete jeho stránku se souhrnem. Tato stránka zobrazuje všechna zobrazení zahrnutá v řešení a poskytuje různé možnosti pro samotné řešení a jeho pracovní prostor. Zobrazení souhrnné stránky řešení pomocí jednoho z výše uvedených postupů zobrazíte seznam řešení a klikněte na název řešení.

![Vlastnosti řešení](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Instalace monitorovacího řešení
Řešení pro monitorování od Microsoftu a partnerů jsou dostupná na [Azure Marketplace](https://azuremarketplace.microsoft.com). Dostupná řešení můžete vyhledat a nainstalovat pomocí následujícího postupu. Při instalaci řešení je nutné vybrat [pracovní prostor Log Analytics,](../platform/manage-access.md) kde bude nainstalováno a kde budou shromážděna jeho data.

1. V [seznamu řešení pro vaše předplatné](#list-installed-monitoring-solutions)klepněte na tlačítko **Přidat**.
1. Vyhledejte nebo vyhledejte řešení. Můžete také procházet řešení z [tohoto odkazu vyhledávání](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Vyhledejte požadované řešení monitorování a přečtěte si jeho popis.
1. Klepnutím na **tlačítko Vytvořit** spusťte proces instalace.
1. Po spuštění procesu instalace budete vyzváni k zadání pracovního prostoru Log Analytics a zadat požadovanou konfiguraci pro řešení.

![Instalace řešení](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalace řešení z komunity
Členové komunity mohou odesílat řešení pro správu do šablon Azure QuickStart. Tato řešení můžete nainstalovat přímo nebo si je stáhnout pro pozdější instalaci.

1. Postupujte podle postupu popsaného v [pracovním prostoru Log Analytics a účet automatizace](#log-analytics-workspace-and-automation-account) propojit pracovní prostor a účet.
2. Přejděte na [šablony azure quickstart .](https://azure.microsoft.com/documentation/templates/) 
3. Vyhledejte řešení, které vás zajímá.
4. Vyberte řešení z výsledků a zobrazte jeho podrobnosti.
5. Klikněte na tlačítko **Nasadit do Azure.**
6. Budete vyzváni k zadání informací, jako je například skupina prostředků a umístění kromě hodnot pro všechny parametry v řešení.
7. Chcete-li nainstalovat řešení, klepněte na tlačítko **Koupit.**


## <a name="log-analytics-workspace-and-automation-account"></a>Účet pracovního prostoru Log Analytics a automatizačního účtu
Všechna řešení monitorování vyžadují [pracovní prostor Log Analytics](../platform/manage-access.md) k ukládání dat shromážděných řešením a k hostování jeho hledání protokolu a zobrazení. Některá řešení také [vyžadují,](../../automation/automation-security-overview.md#automation-account-overview) aby účet Automation obsahoval sady Runbook a související prostředky. Pracovní prostor a účet musí splňovat následující požadavky.

* Každá instalace řešení můžete použít pouze jeden pracovní prostor Log Analytics a jeden účet automatizace. Řešení můžete nainstalovat samostatně do více pracovních prostorů.
* Pokud řešení vyžaduje účet Automatizace, musí být pracovní prostor Analýzy protokolů a účet automatizace vzájemně propojeny. Pracovní prostor Log Analytics může být propojen pouze s jedním účtem Automation a účet Automation může být propojen pouze s jedním pracovním prostorem Log Analytics.
* Chcete-li být propojeny, pracovní prostor Log Analytics a automatizační účet musí být ve stejné skupině prostředků a oblasti. Výjimkou je pracovní prostor v oblasti USA – východ a účet Automatizace v usa – východ 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Vytvoření propojení mezi pracovním prostorem Analýzy protokolů a účtem Automation
Způsob zadání pracovního prostoru Analýzy protokolů a účtu automatizace závisí na způsobu instalace vašeho řešení.

* Když nainstalujete řešení přes Azure Marketplace, budete vyzváni k zadání účtu pracovního prostoru a automatizace. Propojení mezi nimi je vytvořeno, pokud ještě nejsou propojeny.
* Pro řešení mimo Azure Marketplace, musíte propojit pracovní prostor Log Analytics a účet Automatizace před instalací řešení. To můžete provést výběrem libovolného řešení na Azure Marketplace a výběrem pracovního prostoru Log Analytics a účtu Automation. Není nutné skutečně nainstalovat řešení, protože odkaz je vytvořen, jakmile je vybrán pracovní prostor Log Analytics a účet automatizace. Po vytvoření propojení můžete použít tento pracovní prostor Log Analytics a účet automatizace pro libovolné řešení.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi pracovním prostorem Log Analytics a účtem Automation
Propojení mezi pracovním prostorem Log Analytics a účtem Automation můžete ověřit pomocí následujícího postupu.

1. Vyberte účet Automation na webu Azure Portal.
1. Přejděte do části **Související zdroje** v nabídce.
1. Pokud je povoleno nastavení **pracovního prostoru,** je tento účet propojen s pracovním prostorem Analýzy protokolů. Kliknutím na **Pracovní prostor** zobrazíte podrobnosti o pracovním prostoru.

## <a name="remove-a-monitoring-solution"></a>Odebrání monitorovacího řešení
Chcete-li nainstalované řešení odebrat, vyhledejte jej v [seznamu nainstalovaných řešení](#list-installed-monitoring-solutions). Kliknutím na název řešení otevřete jeho stránku souhrnu a poté klikněte na **Odstranit**.


## <a name="next-steps"></a>Další kroky
* Získejte [seznam řešení monitorování od společnosti Microsoft](solutions-inventory.md).
* Zjistěte, jak [vytvářet dotazy](../log-query/log-query-overview.md) k analýze dat shromážděných pomocí řešení monitorování.

