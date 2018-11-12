---
title: Řešení pro správu v Azure | Dokumentace Microsoftu
description: Řešení pro správu v Azure jsou kolekce logiku, vizualizaci, pravidel a získávání dat, které poskytují metriky zaměřené na oblast konkrétního problému.  Tento článek obsahuje informace o instalaci a používání řešení pro správu.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 53020eeb96927143b2fb7394aee64f9b7ea55df9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248958"
---
# <a name="management-solutions-in-azure"></a>Řešení pro správu v Azure
Využijte řešení pro správu služby v Azure a poskytuje další informace o provozu určitá aplikace nebo služby. Tento článek poskytuje stručný přehled o řešení pro správu v Azure a podrobnosti o používání a jejich instalace.

Řešení pro správu obvykle shromažďovat informace do Log Analytics a poskytují prohledávání protokolů a zobrazení pro analýzy shromážděných dat. Může využít i jiné služby, jako je Azure Automation k provedení akce související s aplikací nebo službou.

Řešení pro správu můžete přidat ke svému předplatnému Azure pro všechny aplikace a služby, které používáte. Jsou obvykle dostupné v žádné náklady, ale shromažďovat data, která může vyvolat poplatky za využívání. Kromě řešení od Microsoftu, partneři a zákazníci můžou [vytvořte řešení pro správu](../monitoring/monitoring-solutions-creating.md) k použití ve svém vlastním prostředí nebo k dispozici zákazníkům prostřednictvím komunity.

## <a name="using-management-solutions"></a>Pomocí řešení pro správu
**Přehled** stránky pro každý pracovní prostor Log Analytics se zobrazí dlaždice pro každé řešení v pracovním prostoru nainstalovaná. Kliknutím na dlaždici pro řešení a jeho zobrazení, která zahrnuje podrobnější analýzu otevřete jeho shromážděná data.

![Přehled](media/monitoring-solutions/overview.png)

Řešení pro správu může obsahovat více typů prostředků Azure a můžete zobrazit všechny prostředky získané s řešením, stejně jako jiný prostředek. Například jakékoli hledání v protokolech zahrnutý v řešení jsou součástí **uložená hledání** v pracovním prostoru. Pomocí těchto vyhledávání můžete při provádění analýz ad hoc v Log Analytics.

## <a name="list-installed-management-solutions"></a>Výpis nainstalovaných řešení pro správu 
Seznam řešení pro správu nainstalovaný ve vašem předplatném pomocí následujícího postupu.

1. Přihlaste se k webu Azure Portal.
2. V levém podokně vyberte **všechny služby**.
3. Buď přejděte dolů k položce **řešení** nebo typ *řešení* do **filtr** dialogového okna.
4. Řešení, které jsou nainstalovány ve všech pracovních prostorů jsou uvedeny. Název řešení následuje název pracovního prostoru Log Analytics, který je nainstalován ve.
1. Pomocí pole rozevíracího seznamu v horní části obrazovky můžete filtrovat podle předplatného nebo skupiny prostředků.


![Seznam všech řešení](media/monitoring-solutions/list-solutions-all.png)

Klikněte na název řešení, otevřete její souhrnnou stránku. Tato stránka zobrazuje všechna zobrazení Log Analytics zahrnutý v řešení a nabízí různé možnosti pro řešení samostatně a její pracovní prostor. Zobrazit na souhrnné stránce řešení pomocí jednoho z postupů nad do seznamu řešení a potom klikněte na název řešení.

![Vlastnosti řešení](media/monitoring-solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Nainstalujte řešení pro správu
Řešení pro správu od Microsoftu a partnerů, jsou k dispozici [Azure Marketplace](https://azuremarketplace.microsoft.com). Můžete vyhledat dostupná řešení a nainstalujte je pomocí následujícího postupu.

1. Z [seznam řešení pro vaše předplatné](#list-installed-management-solutions), klikněte na tlačítko **přidat**. 
1. Napravo od **řešení pro správu**, klikněte na tlačítko **Další**. 
1. Vyhledejte řešení pro správu vám vyhovuje a přečtěte si její popis.
1. Klikněte na tlačítko **vytvořit** spustit proces instalace.
1. Při spuštění procesu instalace budete vyzváni k poskytnutí požadované konfigurace, které se liší pro každé řešení. Všechny z nich vyžadují, abyste pracovní prostor Log Analytics vyberte nainstalovanou řešení a kde budou shromažďovány svá data. 

![Instalace řešení](media/monitoring-solutions/install-solution.png)

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
Vyžadovat všechna řešení pro správu [pracovní prostor Log Analytics](../log-analytics/log-analytics-manage-access.md) ukládat data shromážděná tímto řešením a k hostování jeho prohledávání protokolů a zobrazení. Některá řešení také vyžadovat [účtu Automation](../automation/automation-security-overview.md#automation-account-overview) tak, aby obsahovala sady runbook a související prostředky. Pracovní prostor a účet, musí splňovat následující požadavky.

* Každou instalaci řešení lze použít pouze jeden pracovní prostor Log Analytics a jeden účet služby Automation. Řešení můžete nainstalovat samostatně do několika pracovních prostorů.
* Pokud řešení vyžaduje účet Automation, pak pracovní prostor Log Analytics a účet Automation musí být vzájemně propojený. Pracovní prostor Log Analytics může propojit jen do jednoho účtu Automation a účet Automation, které můžou být propojené jenom k jednomu pracovnímu prostoru Log Analytics.
* Propojení, pracovní prostor Log Analytics a účet Automation musí být ve stejné skupině prostředků a oblasti. Výjimkou je pracovní prostor v oblasti USA – východ a účet Automation v oblasti východní USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Vytvoření propojení mezi pracovní prostor Log Analytics a účet služby Automation
Jak určit pracovní prostor Log Analytics a účet Automation závisí na metodě instalace pro vaše řešení.

* Při instalaci řešení prostřednictvím Azure Marketplace, zobrazí se výzva pro pracovní prostor a účet Automation. Pokud již nejsou propojeny se vytvoří propojení mezi nimi.
* Pro řešení mimo Azure Marketplace je třeba propojit pracovní prostor Log Analytics a účet Automation, před instalací řešení. Provedete to výběrem libovolné řešení na webu Azure Marketplace a vyberete pracovní prostor Log Analytics a účet Automation. Není nutné skutečně nainstalovat řešení, protože odkaz je vytvořen poté, co jsou vybrané pracovní prostor Log Analytics a účet Automation. Po vytvoření propojení tohoto pracovního prostoru Log Analytics a účet Automation, můžete použít pro každé řešení.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi pracovní prostor Log Analytics a účet služby Automation
Propojení pracovního prostoru Log Analytics a účet Automation pomocí následujícího postupu můžete ověřit.

1. Vyberte účet Automation na webu Azure Portal.
1. Přejděte **související prostředky** části nabídky.
1. Pokud **pracovní prostor** nastavení povolené, pak tento účet je propojený s pracovním prostorem Log Analytics. Můžete kliknout na **pracovní prostor** zobrazíte podrobnosti o pracovním prostoru.

## <a name="remove-a-management-solution"></a>Odebrat řešení pro správu
Odebrání nainstalované řešení, vyhledejte ho [seznam nainstalovaných řešení](#list-installed-management-solutions). Klikněte na název řešení, otevřete jeho souhrnnou stránku a potom klikněte na **odstranit**.




## <a name="next-steps"></a>Další postup
* Získání [seznam řešení pro správu od Microsoftu](monitoring-solutions-inventory.md).
* Zjistěte, jak [vytvářet dotazy](../log-analytics/log-analytics-queries.md) k analýze dat shromažďovaných řešeními pro správu.

