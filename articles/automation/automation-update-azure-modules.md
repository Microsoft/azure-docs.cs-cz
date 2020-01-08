---
title: Aktualizace modulů Azure v Azure Automation
description: Tento článek popisuje, jak teď můžete aktualizovat běžné Azure PowerShell moduly, které jsou ve výchozím nastavení k dispozici v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3d7eaae452f307b350c111452b819576cf7f17e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420485"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Postup aktualizace modulů Azure PowerShell v Azure Automation

Pokud chcete aktualizovat moduly Azure v účtu Automation, musíte použít [sadu Runbook pro aktualizaci modulů Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), která je k dispozici jako open source. Pokud chcete začít používat sadu Runbook **Update-AutomationAzureModulesForAccount** , abyste mohli aktualizovat moduly Azure, Stáhněte si ho z části [Aktualizace Azure modules Runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) na GitHubu. Pak ho můžete importovat do svého účtu Automation nebo spustit jako skript. Informace o tom, jak importovat Runbook do svého účtu Automation, najdete v tématu [Import Runbooku](manage-runbooks.md#import-a-runbook).

Nejběžnější moduly AzureRM PowerShellu jsou ve výchozím nastavení k dispozici v každém účtu Automation. Tým Azure pravidelně aktualizuje moduly Azure, takže pokud chcete aktualizovat moduly ve vašich účtech Automation, budete potřebovat sadu Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .

Vzhledem k tomu, že moduly jsou pravidelně aktualizovány skupinou produktů, můžou se v zahrnutých rutinách provádět změny. Tato akce může mít negativní vliv na vaše Runbooky v závislosti na typu změny, jako je například přejmenování parametru nebo zaplnění rutiny.

Chcete-li se vyhnout vlivu na vaše Runbooky a procesy, které automatizují, otestujte a ověřte před tím, než budete pokračovat. Pokud nemáte pro tento účel vyhrazený účet služby Automation, zvažte jeho vytvoření, abyste mohli otestovat mnoho různých scénářů během vývoje runbooků. Toto testování by mělo zahrnovat iterativní změny, jako je například aktualizace modulů prostředí PowerShell.

Pokud vaše skripty vyvíjíte místně, doporučujeme mít v místním počítači stejné verze modulu, které máte v účtu Automation při testování, abyste se ujistili, že dostanete stejné výsledky. Po ověření výsledků a provedení všech požadovaných změn můžete změny přesunout do produkčního prostředí.

> [!NOTE]
> Nový účet Automation nemusí obsahovat nejnovější moduly.

> [!NOTE]
> Nebudete moci odstranit globální moduly – moduly, které automatizaci zajišťují.

## <a name="considerations"></a>Požadavky

V následující části jsou některé předpoklady, které je potřeba vzít v úvahu při použití tohoto procesu k aktualizaci modulů Azure:

* Tento Runbook podporuje standardně aktualizaci modulů **Azure** a **AzureRm** . Tento Runbook podporuje také aktualizaci modulů **AZ** . Další informace o aktualizaci `Az`ch modulů pomocí této sady Runbook najdete v [souboru Readme k sadě Runbook pro aktualizaci modulů Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) . Existují další důležité faktory, které je potřeba vzít v úvahu při použití `Az` modulů v účtu Automation. Další informace najdete v tématu [použití AZ modules v účtu Automation](az-modules.md).

* Před zahájením této sady Runbook se ujistěte, že má váš účet Automation vytvořené [přihlašovací údaje účtu Spustit jako pro Azure](manage-runas-account.md) .

* Tento kód můžete použít jako regulární skript PowerShellu místo Runbooku: stačí se nejdřív přihlásit k Azure pomocí příkazu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) a pak předat `-Login $false` skriptu.

* Pokud chcete použít tuto sadu Runbook v cloudech svrchovaného prostředí, použijte parametr `AzureRmEnvironment` pro předání správného prostředí Runbooku.  Přijatelné hodnoty jsou **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**a **AzureUSGovernment**. Tyto hodnoty lze načíst pomocí `Get-AzureRmEnvironment | select Name`. Pokud tomuto parametru nepředáte hodnotu, sada Runbook se nastaví jako výchozí ve veřejném cloudu Azure **AzureCloud**

* Pokud chcete použít konkrétní verzi modulu Azure PowerShell namísto nejnovějšího dostupného Galerie prostředí PowerShell, předejte tyto verze volitelnému parametru `ModuleVersionOverrides` sady Runbook **Update-AutomationAzureModulesForAccount** . Příklady najdete v tématu sada Runbook [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) . Azure PowerShell moduly, které nejsou uvedené v parametru `ModuleVersionOverrides`, se aktualizují s nejnovějšími verzemi modulu na Galerie prostředí PowerShell. Pokud do parametru `ModuleVersionOverrides` nepředáte žádnou hodnotu, všechny moduly se aktualizují s nejnovějšími verzemi modulu v Galerie prostředí PowerShell. Toto chování je stejné jako u tlačítka **aktualizovat moduly Azure** .

## <a name="next-steps"></a>Další kroky

Další informace o této službě najdete v části open source [Update Runbook modules Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .
