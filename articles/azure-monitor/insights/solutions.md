---
title: Monitorování řešení ve službě Azure Monitor | Dokumentace Microsoftu
description: Monitorování řešení ve službě Azure Monitor jsou kolekce logiku, vizualizaci, pravidel a získávání dat, které poskytují metriky zaměřené na oblast konkrétního problému.  Tento článek obsahuje informace o instalaci a používání řešení monitorování.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: ff7d886d3f219d007f159167cacfed1b8ee13863
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001013"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Monitorování řešení ve službě Azure Monitor
Řešení monitorování využití služeb v Azure a poskytuje další informace o provozu určitá aplikace nebo služby. Tento článek poskytuje stručný přehled monitorování řešení v Azure a podrobnosti o používání a jejich instalace.

> [!NOTE]
> Řešení monitorování bylo dříve označované jako řešení pro správu.

Monitorování řešení obvykle shromažďovat data protokolu a poskytují dotazů a zobrazení pro analýzy shromážděných dat. Může využít i jiné služby, jako je Azure Automation k provedení akce související s aplikací nebo službou.

Monitorování řešení můžete přidat do Azure monitoru pro všechny aplikace a služby, které používáte. Jsou obvykle dostupné v žádné náklady, ale shromažďovat data, která může vyvolat poplatky za využívání. Kromě řešení od Microsoftu, partneři a zákazníci můžou [vytvořte řešení pro správu](solutions-creating.md) k použití ve svém vlastním prostředí nebo k dispozici zákazníkům prostřednictvím komunity.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Použití řešení monitorování
Otevřít **přehled** stránky ve službě Azure Monitor k zobrazení dlaždice pro každé řešení v pracovním prostoru nainstalovaná. 

1. Přihlaste se k webu Azure Portal.
1. Otevřít **všechny služby** a vyhledejte **monitorování**.
1. V části **Insights** nabídce vyberte možnost **Další**.
1. Pomocí pole rozevíracího seznamu v horní části obrazovky můžete změnit pracovní prostor nebo časový rozsah pro dlaždice.
1. Kliknutím na dlaždici pro řešení, otevřete jeho zobrazení, která zahrnuje podrobnější analýzu jeho shromážděná data.

![Přehled](media/solutions/overview.png)

Monitorování řešení může obsahovat více typů prostředků Azure a můžete zobrazit všechny prostředky získané s řešením, stejně jako jiný prostředek. Například všechny dotazy log zahrnutý v řešení jsou uvedeny v části **řešení dotazy** v [Průzkumníka dotazů](../log-query/get-started-portal.md#load-queries) tyto dotazy můžete použít při provádění analýz ad hoc s [protokolu dotazy ](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Vypsání seznamu instalovaných řešení monitorování 
Seznam řešení monitorování, které jsou nainstalovány ve vašem předplatném pomocí následujícího postupu.

1. Přihlaste se k webu Azure Portal.
1. Otevřít **všechny služby** a vyhledejte **řešení**.
4. Řešení, které jsou nainstalovány ve všech pracovních prostorů jsou uvedeny. Název řešení následuje název pracovního prostoru, který je nainstalován ve.
1. Pomocí pole rozevíracího seznamu v horní části obrazovky můžete filtrovat podle předplatného nebo skupiny prostředků.


![Seznam všech řešení](media/solutions/list-solutions-all.png)

Klikněte na název řešení, otevřete její souhrnnou stránku. Tato stránka zobrazuje všechna zobrazení zahrnutý v řešení a nabízí různé možnosti pro řešení samostatně a její pracovní prostor. Zobrazit na souhrnné stránce řešení pomocí jednoho z postupů nad do seznamu řešení a potom klikněte na název řešení.

![Vlastnosti řešení](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Nainstalujte řešení monitorování
Monitorování řešení od Microsoftu a partnerů, jsou k dispozici [Azure Marketplace](https://azuremarketplace.microsoft.com). Můžete vyhledat dostupná řešení a nainstalujte je pomocí následujícího postupu. Při instalaci řešení, musíte vybrat [pracovní prostor Log Analytics](../platform/manage-access.md) nainstalovanou řešení a kde budou shromažďovány svá data.

1. Z [seznam řešení pro vaše předplatné](#list-installed-management-solutions), klikněte na tlačítko **přidat**. 
1. Napravo od **řešení pro správu**, klikněte na tlačítko **Další**. 
1. Vyhledejte řešení monitorování vám vyhovuje a přečtěte si její popis.
1. Klikněte na tlačítko **vytvořit** spustit proces instalace.
1. Při spuštění procesu instalace budete vyzváni k poskytnutí požadované konfigurace, které se liší pro každé řešení.

![Instalace řešení](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalace řešení od komunity
Členové komunity můžete odeslat řešení pro správu šablon rychlý start Azure. Můžete nainstalovat přímo na tato řešení nebo si je stáhnout šablony pro pozdější instalaci.

1. Postupujte podle procesu popsaného v [pracovní prostor Log Analytics a účet Automation](#log-analytics-workspace-and-automation-account) propojit pracovní prostor a účet.
2. Přejděte na [šablony rychlý start Azure](https://azure.microsoft.com/documentation/templates/). 
3. Vyhledejte řešení, které vás zajímají.
4. Vyberte řešení ve výsledcích zobrazíte její podrobnosti.
5. Klikněte na tlačítko **nasadit do Azure** tlačítko.
6. Budete vyzváni k zadání informací o například skupinu prostředků a umístění kromě hodnot pro všechny parametry v řešení.
7. Klikněte na tlačítko **nákupní** instalací řešení.


## <a name="log-analytics-workspace-and-automation-account"></a>Pracovní prostor log Analytics a účet služby Automation
Všechna řešení pro monitorování vyžadovat [pracovní prostor Log Analytics](../platform/manage-access.md) ukládat data shromážděná tímto řešením a k hostování jeho prohledávání protokolů a zobrazení. Některá řešení také vyžadovat [účtu Automation](../../automation/automation-security-overview.md#automation-account-overview) tak, aby obsahovala sady runbook a související prostředky. Pracovní prostor a účet, musí splňovat následující požadavky.

* Každou instalaci řešení lze použít pouze jeden pracovní prostor Log Analytics a jeden účet služby Automation. Řešení můžete nainstalovat samostatně do několika pracovních prostorů.
* Pokud řešení vyžaduje účet Automation, pak pracovní prostor Log Analytics a účet Automation musí být vzájemně propojený. Pracovní prostor Log Analytics může propojit jen do jednoho účtu Automation a účet Automation, které můžou být propojené jenom k jednomu pracovnímu prostoru Log Analytics.
* Propojení, pracovní prostor Log Analytics a účet Automation musí být ve stejné skupině prostředků a oblasti. Výjimkou je pracovní prostor v oblasti USA – východ a účet Automation v oblasti východní USA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Vytvořte propojení mezi pracovní prostor Log Analytics a účet služby Automation
Jak určit pracovní prostor Log Analytics a účet Automation závisí na metodě instalace pro vaše řešení.

* Při instalaci řešení prostřednictvím Azure Marketplace, zobrazí se výzva pro pracovní prostor a účet Automation. Pokud již nejsou propojeny se vytvoří propojení mezi nimi.
* Pro řešení mimo Azure Marketplace je třeba propojit pracovní prostor Log Analytics a účet Automation, před instalací řešení. Provedete to výběrem libovolné řešení na webu Azure Marketplace a vyberete pracovní prostor Log Analytics a účet Automation. Není nutné skutečně nainstalovat řešení, protože odkaz je vytvořen poté, co jsou vybrané pracovní prostor Log Analytics a účet Automation. Po vytvoření propojení tohoto pracovního prostoru Log Analytics a účet Automation, můžete použít pro každé řešení.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi pracovní prostor Log Analytics a účet služby Automation
Propojení pracovního prostoru Log Analytics a účet Automation pomocí následujícího postupu můžete ověřit.

1. Vyberte účet Automation na webu Azure Portal.
1. Přejděte **související prostředky** části nabídky.
1. Pokud **pracovní prostor** nastavení povolené, pak tento účet je propojený s pracovním prostorem Log Analytics. Můžete kliknout na **pracovní prostor** zobrazíte podrobnosti o pracovním prostoru.

## <a name="remove-a-monitoring-solution"></a>Odebrat řešení monitorování
Odebrání nainstalované řešení, vyhledejte ho [seznam nainstalovaných řešení](#list-installed-monitoring-solutions). Klikněte na název řešení, otevřete jeho souhrnnou stránku a potom klikněte na **odstranit**.


## <a name="next-steps"></a>Další postup
* Získání [seznam sledování řešení od Microsoftu](solutions-inventory.md).
* Zjistěte, jak [vytvářet dotazy](../log-query/log-query-overview.md) analyzovat data shromážděná službou sledování řešení.

