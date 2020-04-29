---
title: Sestavení řešení pro správu v Azure | Microsoft Docs
description: Mezi řešení pro správu patří operační scénáře správy v Azure, které můžou zákazníci přidat do svého pracovního prostoru Log Analytics.  Tento článek poskytuje podrobné informace o tom, jak můžete vytvářet řešení pro správu, která se mají používat ve vašem vlastním prostředí nebo která budou k dispozici pro vaše zákazníky.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663211"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Návrh a sestavení řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytváření řešení pro správu v Azure, která jsou momentálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.

[Řešení pro správu]( solutions.md) poskytují balíčky pro správu, které můžou zákazníci přidat do svého pracovního prostoru Log Analytics.  Tento článek představuje základní postup pro návrh a sestavení řešení pro správu, které je vhodné pro většinu běžných požadavků.  Pokud s vytvářením řešení pro správu začínáte, můžete tento proces použít jako výchozí bod a pak využít koncepty složitějších řešení v rámci vývoje vašich požadavků.

## <a name="what-is-a-management-solution"></a>Co je řešení pro správu?

Řešení pro správu obsahují prostředky Azure, které vzájemně spolupracují za účelem dosažení konkrétního scénáře správy.  Jsou implementovány jako [šablony správy prostředků](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) , které obsahují podrobné informace o instalaci a konfiguraci obsažených prostředků při instalaci řešení.

Základní strategií je spustit řešení pro správu tím, že se v prostředí Azure sestaví jednotlivé komponenty.  Jakmile funkce fungují správně, můžete je začít zabalit do [souboru řešení pro správu]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Návrh řešení
Nejběžnější vzor řešení pro správu je zobrazený v následujícím diagramu.  Jednotlivé komponenty v tomto vzoru jsou popsány v následující části.

![Přehled řešení pro správu](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Zdroje dat
Prvním krokem při návrhu řešení je určení dat, která požadujete z úložiště Log Analytics.  Tato data můžou být shromažďovaná [zdrojem dat](../../azure-monitor/platform/agent-data-sources.md) nebo [jiným řešením]( solutions.md), nebo může být potřeba, aby vám řešení mohla poskytnout proces jeho shromáždění.

Existuje několik způsobů, jak se v úložišti Log Analytics shromažďovat zdroje dat, jak je popsáno v tématu [zdroje dat v Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  To zahrnuje události v protokolu událostí systému Windows nebo vygenerované protokolem syslog společně s čítači výkonu pro klienty se systémem Windows i Linux.  Data můžete shromažďovat taky z prostředků Azure shromážděných pomocí Azure Monitor.  

Pokud požadujete data, která nejsou přístupná prostřednictvím některého z dostupných zdrojů dat, můžete použít [rozhraní API kolekce dat http](../../azure-monitor/platform/data-collector-api.md) , které umožňuje zapisovat data do úložiště Log Analytics z libovolného klienta, který může volat REST API.  Nejběžnějšími prostředky pro vlastní shromažďování dat v řešení pro správu je vytvoření [Runbooku v Azure Automation](../../automation/automation-runbook-types.md) , který shromažďuje požadovaná data z Azure nebo externích prostředků a používá rozhraní API kolekce dat k zápisu do úložiště.  

### <a name="log-searches"></a>Hledání v protokolu
[Hledání v protokolech](../../azure-monitor/log-query/log-query-overview.md) slouží k extrakci a analýze dat v úložišti Log Analytics.  Jsou používány zobrazeními a výstrahami kromě toho, že uživatel může provádět ad hoc analýzu dat v úložišti.  

Měli byste definovat všechny dotazy, které považujete za užitečné pro uživatele, i když nejsou využívány žádnými zobrazeními a výstrahami.  Budou jim k dispozici jako uložená hledání na portálu a můžete je také zahrnout do [seznamu součástí vizualizace dotazů](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) ve vlastním zobrazení.

### <a name="alerts"></a>Výstrahy
[Výstrahy v Log Analytics](../../azure-monitor/platform/alerts-overview.md) identifikují problémy prostřednictvím [prohledávání protokolů](#log-searches) s daty v úložišti.  Buď upozorní uživatele, nebo automaticky spustí akci v reakci. Pro svou aplikaci byste měli určit různé podmínky pro upozornění a zahrnout odpovídající pravidla výstrahy do souboru řešení.

Pokud se problém může potenciálně vyřešit pomocí automatizovaného procesu, vytvoří se při provádění této nápravy obvykle sada Runbook v Azure Automation.  Většinu služeb Azure je možné spravovat pomocí [rutin](/powershell/azure/overview) , které by sada Runbook mohla využít k provádění takových funkcí.

Pokud vaše řešení vyžaduje externí funkce v reakci na výstrahu, můžete použít [odpověď Webhooku](../../azure-monitor/platform/alerts-metric.md).  To umožňuje volat externí webovou službu, která odesílá informace z výstrahy.

### <a name="views"></a>Zobrazení
Zobrazení v Log Analytics slouží k vizualizaci dat z úložiště Log Analytics.  Každé řešení bude obvykle obsahovat jedno zobrazení s [dlaždicí](../../azure-monitor/platform/view-designer-tiles.md) , která se zobrazí na hlavním řídicím panelu uživatele.  Zobrazení může obsahovat libovolný počet [částí vizualizace](../../azure-monitor/platform/view-designer-parts.md) , které uživateli poskytují různé vizualizace shromážděných dat.

[Vlastní zobrazení můžete vytvořit pomocí návrháře zobrazení](../../azure-monitor/platform/view-designer.md) , který můžete později exportovat pro zařazení do souboru řešení.  


## <a name="create-solution-file"></a>Vytvořit soubor řešení
Jakmile nakonfigurujete a otestujete komponenty, které budou součástí vašeho řešení, můžete [vytvořit soubor řešení]( solutions-solution-file.md).  Komponenty řešení implementujete v [šabloně správce prostředků](../../azure-resource-manager/templates/template-syntax.md) , která obsahuje [prostředek řešení]( solutions-solution-file.md#solution-resource) s vztahy k ostatním prostředkům v souboru.  


## <a name="test-your-solution"></a>Testování řešení
Během vývoje řešení je budete muset nainstalovat a otestovat v pracovním prostoru.  Můžete to provést pomocí kterékoli z dostupných metod pro [otestování a instalaci šablon Správce prostředků](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="publish-your-solution"></a>Publikování řešení
Jakmile dokončíte a otestujete své řešení, můžete je zpřístupnit zákazníkům prostřednictvím následujících zdrojů.

- **Šablony Azure pro rychlý Start**.  [Šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/) jsou sada správce prostředků šablon, kterou nabízí komunita prostřednictvím GitHubu.  Vaše řešení můžete zpřístupnit pomocí následujících informací v [Průvodci příspěvky](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) umožňuje distribuovat a prodávat vaše řešení ostatním vývojářům, nezávislým výrobcům softwaru a odborníkům na IT.  Můžete se dozvědět, jak publikovat řešení, abyste Azure Marketplacei při [publikování a správě nabídky v Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Další kroky
* Naučte se [vytvořit soubor řešení]( solutions-solution-file.md) pro řešení správy.
* Seznamte se s podrobnostmi o [vytváření Azure Resource Manager šablon](../../azure-resource-manager/templates/template-syntax.md).
* Vyhledejte v [šablonách rychlého startu Azure](https://azure.microsoft.com/documentation/templates) ukázky různých šablon Správce prostředků.
