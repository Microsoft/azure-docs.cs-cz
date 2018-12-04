---
title: Vytvoření řešení správy v Azure | Dokumentace Microsoftu
description: Řešení pro správu zahrnují scénáře správy zabalené v Azure, Zákazníci můžete přidat do svého pracovního prostoru Log Analytics.  Tento článek obsahuje podrobné informace o tom, jak můžete vytvořit řešení pro správu pro použití ve vašem prostředí nebo k dispozici zákazníkům.
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
ms.openlocfilehash: 2c842efd6c66fd70bbac3969090b71c42c725545
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839632"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Návrh a vytváření řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Žádné schéma je popsáno níže se může změnit.

[Řešení pro správu]( solutions.md) poskytují scénáře zabalené správy, které zákazníci můžete přidat do svého pracovního prostoru Log Analytics.  Tento článek představuje základní proces navrhovat a sestavovat řešení pro správu, který je vhodný pro nejčastějších požadavků.  Pokud jste ještě na vytváření řešení pro správu můžete použít tento proces jako výchozí bod a potom využít koncepty pro složitější řešení, jak se vyvíjí vaše požadavky.

## <a name="what-is-a-management-solution"></a>Co je řešení pro správu?

Řešení pro správu obsahovat prostředky Azure, které spolupracují a dosáhnout konkrétní scénáře správy.  Jsou implementované jako [šablony pro správu prostředků](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) , které obsahují podrobnosti o tom, jak nainstalovat a nakonfigurovat jejich obsažených prostředků při instalaci řešení.

Základní strategií je řešení pro správu Začínáme integrováním jednotlivých komponent v prostředí Azure.  Jakmile budete mít funkce funguje správně, pak můžete začít balení do [soubor řešení správy]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Navrhněte svoje řešení
Nejběžnější vzor jako řešení pro správu je znázorněno v následujícím diagramu.  Různé součásti v tomto vzoru jsou popsány v níže.

![Přehled řešení správy](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Zdroje dat
Prvním krokem při navrhování řešení je určení data, která budete potřebovat v úložišti Log Analytics.  Tato data mohou být shromážděné [zdroj dat](../../azure-monitor/platform/agent-data-sources.md) nebo [jiné řešení]( solutions.md), nebo řešení může být nutné zadat proces shromažďování vyžadováno.

Existuje mnoho způsobů, zdroje dat, které můžete shromážděných v úložišti Log Analytics, jak je popsáno v [zdroje dat v Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  To zahrnuje události v protokolu událostí Windows nebo generovaných Syslog kromě čítače výkonu pro klienty Windows a Linux.  Může také shromažďovat data z prostředků Azure, které jsou shromážděné službou Azure Monitor.  

Pokud potřebujete data, která není dostupný pomocí některého z dostupných datových zdrojů, pak můžete použít [rozhraní API kolekce dat HTTP](../../log-analytics/log-analytics-data-collector-api.md) která umožňuje zápis dat do úložiště Log Analytics z libovolného klienta, která může volat rozhraní REST API.  Nejčastěji používané prostředky kolekce vlastních dat v řešení pro správu, je vytvořit [sady runbook ve službě Azure Automation](../../automation/automation-runbook-types.md) , který shromažďuje požadovaná data z Azure nebo externích prostředků a používá k zápisu do rozhraní API kolekce dat úložiště.  

### <a name="log-searches"></a>Prohledávání protokolů
[Prohledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) slouží k extrahování a analýzy dat v úložišti Log Analytics.  Používají se v zobrazeních a výstrahy navíc umožňuje uživateli provádět ad hoc analýzy dat v úložišti.  

Byste měli definovat žádné dotazy, které si myslíte, že bude pro uživatele užitečné, i v případě, že nejsou použity žádné zobrazení nebo výstrahy.  To budou k dispozici jako uložená hledání na portálu, a můžete je použít také [části vizualizace dotazy seznamu](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) vlastní zobrazení.

### <a name="alerts"></a>Výstrahy
[Upozornění v Log Analytics](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) identifikovat problémy prostřednictvím [prohledávání protokolů](#log-searches) s daty v úložišti.  Jsou buď upozornit uživatele, nebo automaticky spustit akci v odpovědi. By měl určit různé podmínky upozornění pro vaši aplikaci a zahrnout do souboru řešení odpovídající pravidla upozornění.

Pokud tento problém můžete opravit potenciálně pomocí automatizovaného procesu, pak obvykle vytvoříte sady runbook ve službě Azure Automation, abyste mohli provést tento nápravu.  Většina služeb Azure je možné spravovat pomocí [rutiny](/powershell/azure/overview) kterém sada runbook by využívat k provádění těchto funkcí.

Pokud vaše řešení nevyžaduje externí funkce v reakci na výstrahu a pak můžete použít [odpověď webhooku](../../monitoring-and-diagnostics/alert-metric.md).  To umožňuje volání externí webové služby, odesílání informací z výstrahy.

### <a name="views"></a>Zobrazení
Zobrazení v Log Analytics se používají k vizualizaci dat v úložišti Log Analytics.  Každé řešení bude obvykle obsahovat na jednom místě [dlaždici](../../azure-monitor/platform/view-designer-tiles.md) , která se zobrazí v hlavním řídicím panelu uživatele.  Zobrazení může obsahovat libovolný počet [části vizualizace](../../azure-monitor/platform/view-designer-parts.md) poskytovat různé vizualizace shromážděných dat uživatele.

Můžete [vytváření vlastních zobrazení pomocí návrháře zobrazení](../../azure-monitor/platform/view-designer.md) který můžete později to taky pro zahrnutí do souboru řešení.  


## <a name="create-solution-file"></a>Vytvořit soubor řešení
Po nakonfigurování a testovat komponenty, které budou součástí vašeho řešení, můžete [vytvořit soubor řešení]( solutions-solution-file.md).  Budete implementovat řešení komponenty [šablony Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md) , který obsahuje [prostředků řešení]( solutions-solution-file.md#solution-resource) se vztahy k jiným prostředkům v souboru.  


## <a name="test-your-solution"></a>Otestování řešení
Při vývoji řešení, je potřeba nainstalovat a otestovat ho ve vašem pracovním prostoru.  Můžete provést některou z dostupných metod pro [testování a instalace šablon Resource Manageru](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publikování řešení
Po dokončení a testování vašeho řešení, můžete zpřístupnit je pro zákazníky prostřednictvím následujících zdrojů.

- **Šablony Azure Quickstart**.  [Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/) je sada přispěla komunita přes GitHub šablon Resource Manageru.  Můžete zpřístupnit vaše řešení podle následujících informací v [průvodci pro přispěvatele](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) vám umožní distribuovat a prodávejte své řešení s ostatními vývojáři, nezávislým výrobcům softwaru a odborníci na IT.  Můžete informace o publikování řešení na webu Azure Marketplace na [jak publikovat a spravovat nabídky na webu Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvořit soubor řešení]( solutions-solution-file.md) pro vaše řešení pro správu.
* Další podrobnosti o [šablon pro vytváření Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md).
* Hledání [šablony pro rychlý start Azure](https://azure.microsoft.com/documentation/templates) ukázky různých šablon Resource Manageru.
