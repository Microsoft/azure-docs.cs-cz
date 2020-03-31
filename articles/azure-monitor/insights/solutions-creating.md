---
title: Vytvoření řešení pro správu v Azure | Dokumenty společnosti Microsoft
description: Řešení pro správu zahrnují scénáře sbalených správou v Azure, které mohou zákazníci přidat do svého pracovního prostoru Log Analytics.  Tento článek obsahuje podrobnosti o tom, jak můžete vytvořit řešení pro správu, která budou použita ve vašem vlastním prostředí nebo zpřístupněna zákazníkům.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663211"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Návrh a vytvoření řešení pro správu v Azure (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu v Azure, které jsou aktuálně ve verzi preview. Jakékoli schéma popsané níže se může změnit.

[Řešení pro správu]( solutions.md) poskytují scénáře sbalené správy, které mohou zákazníci přidat do svého pracovního prostoru Log Analytics.  Tento článek představuje základní proces návrhu a sestavení řešení pro správu, které je vhodné pro většinu běžných požadavků.  Pokud s řešením správy budov tečujete, můžete tento proces použít jako výchozí bod a poté využít koncepty pro složitější řešení, jak se vaše požadavky vyvíjejí.

## <a name="what-is-a-management-solution"></a>Co je řešení pro správu?

Řešení pro správu obsahují prostředky Azure, které spolupracují na dosažení konkrétního scénáře správy.  Jsou implementovány jako [šablony správy prostředků,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) které obsahují podrobnosti o tom, jak nainstalovat a nakonfigurovat jejich obsažené prostředky při instalaci řešení.

Základní strategií je spuštění řešení pro správu vytvořením jednotlivých komponent ve vašem prostředí Azure.  Jakmile budete mít funkce pracovat správně, pak můžete začít balení je do [souboru řešení pro správu]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Navrhněte své řešení
Nejběžnější vzor pro řešení správy je znázorněno v následujícím diagramu.  Různé součásti v tomto vzoru jsou popsány v níže.

![Přehled řešení pro správu](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Zdroje dat
Prvním krokem při navrhování řešení je určení dat, které požadujete z úložiště Log Analytics.  Tato data mohou být shromažďována [zdrojem dat](../../azure-monitor/platform/agent-data-sources.md) nebo [jiným řešením]( solutions.md)nebo vaše řešení může potřebovat poskytnout proces jejich shromažďování.

Existuje celá řada způsobů, jak zdroje dat, které lze shromažďovat v úložišti Log Analytics, jak je popsáno v [zdrojích dat v Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  To zahrnuje události v protokolu událostí systému Windows nebo generované Syslog kromě čítače výkonu pro klienty Windows a Linux.  Můžete také shromažďovat data z prostředků Azure shromážděných službou Azure Monitor.  

Pokud požadujete data, která nejsou přístupná prostřednictvím žádného z dostupných zdrojů dat, můžete použít [rozhraní API shromažďování dat PROTOKOLU HTTP,](../../azure-monitor/platform/data-collector-api.md) které umožňuje zapisovat data do úložiště Log Analytics z libovolného klienta, který může volat rozhraní REST API.  Nejběžnější prostředky vlastní shromažďování dat v řešení pro správu je vytvořit [runbook v Azure Automation,](../../automation/automation-runbook-types.md) který shromažďuje požadovaná data z Azure nebo externích prostředků a používá rozhraní API pro sběr dat pro zápis do úložiště.  

### <a name="log-searches"></a>Vyhledávání protokolů
[Vyhledávání protokolů](../../azure-monitor/log-query/log-query-overview.md) se používá k extrahování a analýze dat v úložišti Log Analytics.  Kromě toho, že uživateli umožňují provádět analýzu dat ad hoc v úložišti, je používají také zobrazení a výstrahy.  

Měli byste definovat všechny dotazy, které si myslíte, že bude užitečné pro uživatele i v případě, že nejsou používány žádné zobrazení nebo výstrahy.  Ty budou k dispozici jako uložená hledání na portálu a můžete je také zahrnout do [vizualizační části Seznam dotazů](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) ve vlastním zobrazení.

### <a name="alerts"></a>Výstrahy
[Výstrahy v Log Analytics](../../azure-monitor/platform/alerts-overview.md) identifikovat problémy prostřednictvím [protokolu vyhledávání](#log-searches) proti data v úložišti.  Buď upozorní uživatele, nebo automaticky spustí akci jako odpověď. Měli byste určit různé podmínky výstrahy pro vaši aplikaci a zahrnout odpovídající pravidla výstrah v souboru řešení.

Pokud problém může být potenciálně opraveny pomocí automatizovaného procesu, pak obvykle vytvoříte runbook v Azure Automation k provedení této nápravy.  Většinu služeb Azure lze spravovat pomocí [rutin,](/powershell/azure/overview) které by runbook využil k provádění těchto funkcí.

Pokud vaše řešení vyžaduje externí funkce v reakci na výstrahu, můžete použít [webhooku odpověď](../../azure-monitor/platform/alerts-metric.md).  To umožňuje volat externí webovou službu odesílající informace z výstrahy.

### <a name="views"></a>Zobrazení
Zobrazení v Log Analytics se používají k vizualizaci dat z úložiště Log Analytics.  Každé řešení bude obvykle obsahovat jedno zobrazení s [dlaždicí,](../../azure-monitor/platform/view-designer-tiles.md) která je zobrazena na hlavním řídicím panelu uživatele.  Zobrazení může obsahovat libovolný počet [vizualizačních částí,](../../azure-monitor/platform/view-designer-parts.md) které uživateli poskytují různé vizualizace shromážděných dat.

[Vlastní zobrazení vytvoříte pomocí Návrháře zobrazení,](../../azure-monitor/platform/view-designer.md) který můžete později exportovat pro zahrnutí do souboru řešení.  


## <a name="create-solution-file"></a>Vytvořit soubor řešení
Po konfiguraci a testování součástí, které budou součástí vašeho řešení, můžete [vytvořit soubor řešení]( solutions-solution-file.md).  Součásti řešení implementujete v [šabloně Správce prostředků,](../../azure-resource-manager/templates/template-syntax.md) která obsahuje [prostředek řešení]( solutions-solution-file.md#solution-resource) se vztahy k ostatním prostředkům v souboru.  


## <a name="test-your-solution"></a>Otestujte své řešení
Při vývoji řešení budete muset nainstalovat a otestovat v pracovním prostoru.  Můžete to provést pomocí některé z dostupných metod pro [testování a instalaci šablon Správce prostředků](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="publish-your-solution"></a>Publikování řešení
Po dokončení a testování řešení můžete zpřístupnit zákazníkům buď z následujících zdrojů.

- **Šablony Azure Quickstart**.  [Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/) je sada šablon Správce prostředků, které komunita prostřednictvím GitHubu přispívá.  Řešení můžete zpřístupnit pomocí následujících informací v [průvodci příspěvkem](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) umožňuje distribuovat a prodávat vaše řešení dalším vývojářům, nevlastním nasazení a it profesionálům.  Jak publikovat své řešení na Azure Marketplace na jdete [publikovat a spravovat nabídku na Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [vytvořit soubor řešení]( solutions-solution-file.md) pro řešení pro správu.
* Přečtěte si podrobnosti o [vytváření šablon Azure Resource Manager .](../../azure-resource-manager/templates/template-syntax.md)
* Prohledejte [šablony Azure QuickStart](https://azure.microsoft.com/documentation/templates) pro ukázky různých šablon Správce prostředků.
