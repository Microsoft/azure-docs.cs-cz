---
title: Vytvoření řešení správy v Azure | Microsoft Docs
description: Řešení pro správu zahrnují scénářů zabalené správy v Azure, které zákazníci mohou přidat jejich pracovní prostor analýzy protokolů.  Tento článek poskytuje podrobné informace o tom, jak můžete vytvořit řešení správy, který se má použít ve svém vlastním prostředí nebo k dispozici pro vaše zákazníky.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92089904941ae913f1992a4407083bfcae010f2d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Návrh a vytvoření řešení správy v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytvoření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Žádné schéma popsané níže se mohou změnit.

[Řešení pro správu]( monitoring-solutions.md) zadejte scénářů zabalené správy, které zákazníci mohou přidat do své pracovní prostor analýzy protokolů.  Tento článek představuje základní proces návrh a vytvoření řešení správy, který je vhodný pro nejběžnější požadavky.  Pokud začínáte vytváření řešení pro správu můžete tento proces použijte jako výchozí bod a pak využít koncepty pro složitější řešení jako momentální vašim požadavkům.

## <a name="what-is-a-management-solution"></a>Co je řešení pro správu?

Řešení pro správu obsahovat prostředky Azure, které spolupracují v zájmu dosažení scénář konkrétní správy.  Jsou implementovány jako [Správa prostředků šablony](../azure-resource-manager/resource-manager-template-walkthrough.md) obsahující podrobnosti o tom, jak nainstalovat a nakonfigurovat jejich obsažených prostředků při instalaci řešení.

Základní strategie je spustit řešení pro správu podle budovy jednotlivých součástí v prostředí Azure.  Jakmile máte funkce funguje správně, potom můžete spustit do balení [soubor řešení správy]( monitoring-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Navrhněte svoje řešení
Nejběžnější vzor jako řešení pro správu je znázorněno v následujícím diagramu.  Různé součásti v tomto vzoru se zabývá níže.

![Přehled řešení správy](media/monitoring-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Zdroje dat
Prvním krokem při navrhování řešení je určení data, která vyžadují z úložiště analýzy protokolů.  Tato data můžou shromáždit pomocí [zdroj dat](../log-analytics/log-analytics-data-sources.md) nebo [jiné řešení]( monitoring-solutions.md), nebo řešení možná muset zadat proces shromažďování ho.

Existuje několik způsobů zdrojů dat, které můžou shromažďovat úložiště analýzy protokolů, jak je popsáno v [zdroje dat v analýzy protokolů](../log-analytics/log-analytics-data-sources.md).  To zahrnuje události v protokolu událostí systému Windows nebo generované Syslog kromě čítače výkonu pro klienty systému Windows a Linux.  Můžete také shromažďovat data z prostředků Azure, které shromažďují Azure monitorování.  

Pokud požadujete data, která není přístupná prostřednictvím některý ze zdrojů dat k dispozici, pak můžete použít [rozhraní API sady kolekcí dat protokolu HTTP](../log-analytics/log-analytics-data-collector-api.md) které umožňuje zapsat data do úložiště analýzy protokolů z libovolného klienta, který můžete volat rozhraní REST API.  Nejběžnější znamená kolekce vlastních dat v rámci řešení pro správu je vytvoření [runbook ve službě Azure Automation](../automation/automation-runbook-types.md) který shromažďuje požadovaná data z Azure nebo externí prostředky a používá rozhraní API sady kolekcí dat k zápisu do úložiště.  

### <a name="log-searches"></a>Protokol hledání
[Přihlaste se hledání](../log-analytics/log-analytics-log-searches.md) slouží k extrakci a analyzovat data v úložišti analýzy protokolů.  Používají se v zobrazeních a výstrahy kromě toho, že uživatel provádět ad hoc analýzu dat v úložišti.  

Měli byste všechny dotazy, které si myslíte, že bude pro uživatele, i když nejsou použity žádné zobrazení nebo výstrahy.  Budou to pro ně k dispozici jako uložená hledání na portálu, a může zahrnovat i je [seznamu dotazy vizualizace část](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) ve vaší vlastní zobrazení.

### <a name="alerts"></a>Výstrahy
[Výstrahy v analýzy protokolů](../log-analytics/log-analytics-alerts.md) identifikovat problémy prostřednictvím [protokolu hledání](#log-searches) s daty v úložišti.  Jsou buď upozornit uživatele, nebo automaticky spustit akci v odpovědi. Abyste uvedli jinou výstrahy podmínky pro aplikaci a zahrnout do souboru řešení odpovídající pravidla výstrah.

Pokud tento problém lze napravit potenciálně s automatizovaného procesu, poté obvykle vytvoříte sady runbook ve službě Azure Automation k provedení této nápravy.  Většina Azure services lze spravovat pomocí [rutiny](/powershell/azure/overview) kterém sada runbook by využít také k provedení takových funkce.

Pokud vaše řešení vyžaduje externí funkce v reakci na výstrahy, pak můžete použít [webhooku odpovědi](../log-analytics/log-analytics-alerts-actions.md).  To umožňuje volání externí webovou službu odesílání informací ve výstraze.

### <a name="views"></a>Zobrazení
Zobrazení analýzy protokolů se používají k vizualizaci dat z úložiště analýzy protokolů.  Každé řešení bude obvykle obsahovat jediné zobrazení s [dlaždici](../log-analytics/log-analytics-view-designer-tiles.md) , se zobrazí na hlavním řídicím uživatele.  Zobrazení může obsahovat libovolný počet [vizualizace částí](../log-analytics/log-analytics-view-designer-parts.md) zajistit různé vizualizace shromážděná data pro uživatele.

Můžete [vytvářet vlastní zobrazení pomocí návrháře zobrazení](../log-analytics/log-analytics-view-designer.md) který můžete později exportovat zahrnuty v souboru řešení.  


## <a name="create-solution-file"></a>Vytvořit soubor řešení
Po nakonfigurování a otestovat součásti, které budou součástí vašeho řešení, můžete [vytvořte svůj soubor řešení]( monitoring-solutions-solution-file.md).  Budete implementovat řešení komponenty [šablony Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) , který obsahuje [řešení prostředků]( monitoring-solutions-solution-file.md#solution-resource) se vztahy k jiným prostředkům v souboru.  


## <a name="test-your-solution"></a>Testování řešení
Při vývoji řešení, musíte se instalovat a testovat v pracovním prostoru.  To provedete pomocí libovolné dostupné metody pro [testování a instalace šablon Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publikování řešení
Po dokončení a testovat řešení, vám může být k dispozici zákazníkům prostřednictvím následujících zdrojů.

- **Šablony Azure rychlý Start**.  [Šablony Azure rychlý Start](https://azure.microsoft.com/resources/templates/) je sada šablon Resource Manageru z komunity přes GitHub.  Můžete zpřístupnit řešení následujících informací do [příspěvku průvodce](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) umožňuje distribuci a prodal řešení jinými vývojáři nezávislí výrobci softwaru a IT profesionály.  Můžete zjistěte, jak publikovat řešení do Azure Marketplace v [publikování a správě nabídku v Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvořte soubor řešení]( monitoring-solutions-solution-file.md) řešení pro správu.
* Další podrobnosti o [šablon pro tvorbu Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).
* Hledání [šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates) ukázky různých šablonách Resource Manager.