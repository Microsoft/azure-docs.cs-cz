---
title: Řešení pro správu v Azure | Microsoft Docs
description: Řešení pro správu v Azure jsou kolekce logiku, vizualizace a data pořízení pravidel, které poskytují metriky seskupit kolem oblasti konkrétní problém.  Tento článek obsahuje informace o instalaci a použití řešení pro správu.
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
ms.openlocfilehash: 3377a0b4e6440d83962d103b3e1770ccf43bd785
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752917"
---
# <a name="management-solutions-in-azure"></a>Řešení pro správu v Azure
Řešení pro správu využívají služby v Azure a poskytnout další aspekty operaci určitá aplikace nebo služby. Tento článek poskytuje stručný přehled řešení pro správu ve službě Azure a podrobnosti o používání a nainstalujete.

Řešení pro správu obvykle shromažďovat informace do analýzy protokolů a zadejte protokolu vyhledávání a zobrazení k analýze shromážděná data. Může využít i jiné služby, jako je například k provádění akcí týkající se aplikace nebo služby Azure Automation.

Řešení pro správu můžete přidat do vašeho předplatného Azure pro všechny aplikace a služby, které používáte. Jsou obvykle dostupná na adrese žádné náklady, ale shromažďování dat, který může vyvolat poplatky za používání. Kromě řešení od společnosti Microsoft, partnery a zákazníky, můžete [vytvořit řešení pro správu](../monitoring/monitoring-solutions-creating.md) k použití ve svém vlastním prostředí nebo přístupná zákazníkům prostřednictvím komunitou.

## <a name="using-management-solutions"></a>Pomocí řešení pro správu
**Přehled** stránky pro každý pracovní prostor analýzy protokolů zobrazí dlaždice pro každé řešení nainstalován v pracovním prostoru. Klikněte na dlaždici pro řešení otevřete jeho zobrazení, která obsahuje více podrobné analýzy jeho shromážděná data.

![Přehled](media/monitoring-solutions/overview.png)

Řešení pro správu může obsahovat více typů prostředků Azure a můžete zobrazit všechny prostředky součástí řešení stejně jako jiný prostředek. Například všechny protokolu hledání součástí řešení jsou součástí **uložená hledání** v pracovním prostoru. Při provádění analýz ad hoc v analýzy protokolů můžete použít tyto hledání.

## <a name="list-installed-management-solutions"></a>Výpis nainstalovaných řešení pro správu 
Seznam řešení pro správu nainstalované v rámci vašeho předplatného použijte následující postup.

1. Přihlaste se k webu Azure Portal.
2. V levém podokně vyberte **všechny služby**.
3. Buď přejděte dolů k položce **řešení** nebo typ *řešení* do **filtru** dialogové okno.
4. Řešení, které jsou nainstalovány ve všech vašich pracovních prostorů, jsou uvedeny. Název řešení následuje název pracovního prostoru analýzy protokolů, který je nainstalován v.
1. Pomocí rozevíracího seznamu polí v horní části obrazovky můžete filtrovat podle předplatné nebo prostředek skupiny.


![Zobrazí seznam všech řešení](media/monitoring-solutions/list-solutions-all.png)

Klikněte na název řešení otevřete stránku s jeho souhrnu. Tato stránka zobrazuje všechny analýzy protokolů zobrazení, který je součástí řešení a nabízí různé možnosti pro řešení samostatně a jeho pracovního prostoru. Zobrazit na souhrnné stránce pro řešení pomocí jednoho z postupů výše na seznamu řešení a potom klikněte na název řešení.

![Vlastnosti řešení](media/monitoring-solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Nainstalujte řešení pro správu
Řešení pro správu od společnosti Microsoft a partneři jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com). Můžete vyhledat dostupná řešení a nainstalujete pomocí následujícího postupu.

1. Z [seznam řešení pro vaše předplatné](#list-installed-management-solutions), klikněte na tlačítko **přidat**. 
1. Napravo od **řešení pro správu**, klikněte na tlačítko **Další**. 
1. Vyhledejte řešení pro správu chcete a pročtěte jeho popis.
1. Klikněte na tlačítko **vytvořit** ke spuštění procesu instalace.
1. Při spuštění procesu instalace se zobrazí výzva k poskytování požadované konfigurace, který se liší pro každé řešení. Všechny z nich vyžadují, abyste vyberte pracovní prostor analýzy protokolů nainstalovanou řešení a kde budou shromažďovány jeho data. 

![Instalaci řešení](media/monitoring-solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalace řešení od komunity
Členové komunity můžete odeslat řešení pro správu šablon Azure rychlý start. Můžete nainstalovat těmito řešeními přímo nebo je stáhnout šablony pro pozdější instalaci.

1. Postupujte podle procesu popsaného v tématu [pracovní prostor analýzy protokolů a účet Automation](#log-analytics-workspace-and-automation-account) propojit pracovní prostor a účet.
2. Přejděte na [šablony Azure rychlý Start](https://azure.microsoft.com/documentation/templates/). 
3. Hledání řešení, které vás zajímají.
4. Vyberte řešení z výsledků zobrazíte její podrobnosti.
5. Klikněte **nasadit do Azure** tlačítko.
6. Zobrazí se výzva k zadání informace, jako je skupina prostředků a umístění kromě hodnoty všech parametrů v řešení.
7. Klikněte na tlačítko **nákupu** instalací řešení.


## <a name="log-analytics-workspace-and-automation-account"></a>Pracovní prostor analýzy protokolů a účet Automation.
Vyžadovat všechna řešení pro správu [pracovní prostor analýzy protokolů](../log-analytics/log-analytics-manage-access.md) ukládat data shromážděná řešení a pro hostování své protokolu vyhledávání a zobrazení. Některá řešení také vyžadují [účet Automation](../automation/automation-security-overview.md#automation-account-overview) tak, aby obsahovala sady runbook a související prostředky. Pracovní prostor a účet, musí splňovat následující požadavky.

* Instalace jednotlivých řešení lze použít pouze jeden pracovní prostor analýzy protokolů a jeden účet Automation. Řešení můžete nainstalovat samostatně do více pracovních prostorů.
* Pokud řešení vyžaduje účet služby Automation, pak pracovní prostor analýzy protokolů a účet Automation musí být propojena na sebe navzájem. Pracovní prostor analýzy protokolů může propojit jen s jeden účet Automation a účet Automation může propojit jen do jednoho pracovního prostoru analýzy protokolů.
* Propojení, pracovní prostor analýzy protokolů a účet Automation musí být ve stejné skupině prostředků a oblast. Výjimkou je pracovního prostoru v oblasti Východ USA a účet Automation v oblasti Východ USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Vytvoření propojení mezi pracovní prostor analýzy protokolů a účet Automation.
Jak zadejte pracovní prostor analýzy protokolů a účet Automation, závisí na metodě instalace pro vaše řešení.

* Když instalujete řešení prostřednictvím Azure Marketplace, zobrazí se výzva pro pracovní prostor a účet Automation. Pokud již nejsou propojené vytvoří propojení mezi nimi.
* Pro řešení mimo Azure Marketplace je nutné před instalací řešení propojit pracovní prostor analýzy protokolů a účet Automation. Můžete provést zvolením řešení v Azure Marketplace a vyberete pracovní prostor analýzy protokolů a účet Automation. Nemáte skutečně nainstalovat řešení, protože odkaz se vytvoří, jakmile jsou vybrané pracovní prostor analýzy protokolů a účet Automation. Po vytvoření odkazu, tento pracovní prostor analýzy protokolů a účet Automation můžete použít pro žádné řešení.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi pracovní prostor analýzy protokolů a účet Automation.
Můžete ověřit propojení mezi pracovní prostor analýzy protokolů a účtu Automation pomocí následujícího postupu.

1. Vyberte účet Automation na portálu Azure.
1. Přejděte k položce **související prostředky** části nabídky.
1. Pokud **prostoru** je povolené nastavení a pak tento účet je propojený s pracovní prostor analýzy protokolů. Kliknutím na **prostoru** k zobrazení podrobností o pracovním prostoru.

## <a name="remove-a-management-solution"></a>Odebrat řešení pro správu
Chcete-li odebrat nainstalované řešení, vyhledejte ji v [seznam nainstalovaných řešení](#list-installed-management-solutions). Klikněte na název řešení otevřete stránku s jeho souhrn a pak klikněte na **odstranit**.




## <a name="next-steps"></a>Další postup
* Získání [seznam řešení pro správu od společnosti Microsoft](monitoring-solutions-inventory.md).
* Zjistěte, jak [vytváření dotazů](../log-analytics/log-analytics-log-searches.md) analyzovat data shromažďovaná společností řešení pro správu.

