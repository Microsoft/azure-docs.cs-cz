---
title: Aktualizace modulů Azure PowerShell u Azure Automation
description: Tento článek popisuje, jak teď můžete aktualizovat běžné moduly Azure PowerShell u předpokladu, které ve výchozím nastavení v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769674"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Aktualizace modulů Azure PowerShell u Azure Automation

Chcete-li aktualizovat moduly Azure v účtu Automation, musíte použít [runbook modulů Update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), který je k dispozici jako open source. Pokud chcete začít používat runbook **Update-AutomationAzureModulesForAccount** k aktualizaci modulů Azure, stáhněte si ho z [úložiště modulů Runbook update Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) na GitHubu. Potom jej můžete importovat do účtu Automation nebo jej spustit jako skript. Informace o tom, jak importovat runbook v účtu Automation, najdete v [tématu Import sady Runbook](manage-runbooks.md#importing-a-runbook).

Nejběžnější moduly prostředí PowerShell jsou k dispozici ve výchozím nastavení v každém účtu automatizace. Tým Azure pravidelně aktualizuje moduly Azure. Proto chcete-li udržovat moduly v účtech automatizace aktuální, měli byste použít [update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook.

Vzhledem k tomu, že moduly jsou pravidelně aktualizovány skupinou produktů, může dojít ke změnám s přiloženými rutinami. Tyto změny, například přejmenování parametru nebo zcela zastaralé rutiny, může negativně ovlivnit vaše runbooky.

Abyste se vyhnuli ovlivnění runbooků a procesů, které automatizují, otestujte a ověřte, než budete pokračovat. Pokud nemáte vyhrazený účet automatizace určený pro tento účel, zvažte jeho vytvoření, abyste mohli otestovat mnoho různých scénářů během vývoje vašich runbooků. Toto testování by mělo zahrnovat iterativní změny, jako je například aktualizace modulů prostředí PowerShell.

Pokud vyvíjíte skripty místně, doporučujeme mít stejné verze modulu místně, které máte ve svém účtu Automation při testování, abyste zajistili, že obdržíte stejné výsledky. Po ověření výsledků a použití všech požadovaných změn můžete změny přesunout do produkčního prostředí.

> [!NOTE]
> Nový účet automatizace nemusí obsahovat nejnovější moduly.

> [!NOTE]
> Nebude možné odstranit globální moduly, což jsou moduly, které automatizace poskytuje po vybalení.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Požadavky

Níže jsou uvedeny některé důležité informace, které je třeba vzít v úvahu při použití tohoto článku k aktualizaci modulů Azure:

* Runbook popsaný v tomto článku podporuje aktualizaci modulů Azure, AzureRM a Az ve výchozím nastavení. Další informace o aktualizaci modulů Az.Automation pomocí této sady runbook naleznete v [modulu Runbook readbooku Update Azure.](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) Při používání modulů Az v účtu Automation je třeba vzít v úvahu další důležité faktory. Další informace najdete [v tématu Správa modulů v Azure Automation](shared-resources/modules.md).

* Před spuštěním tohoto runbooku se ujistěte, že váš účet Automation má vytvořené [přihlašovací údaje účtu Azure Run As.](manage-runas-account.md)

* Tento kód můžete použít jako běžný skript PowerShellu namísto runbooku: stačí se nejprve přihlásit `-Login $false` do Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) a pak předat skriptu.

* Chcete-li použít tento runbook na `AzEnvironment` suverénní chabé mraky, použijte parametr předat správné prostředí runbook.  Přijatelné hodnoty jsou AzureCloud (veřejný cloud Azure), AzureChinaCloud, AzureGermanCloud a AzureUSGovernment. Tyto hodnoty lze načíst pomocí `Get-AzEnvironment | select Name`. Pokud této rutině nepředáte hodnotu, runbook se ve výchozím nastavení přenese na AzureCloud.

* Pokud chcete použít konkrétní verzi modulu Azure PowerShell namísto nejnovějšího modulu dostupného v `ModuleVersionOverrides` Galerii PowerShellu, předajte tyto verze volitelnému parametru runbooku **Update-AutomationAzureModulesForAccount.** Příklady naleznete [aktualizace AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Moduly Azure PowerShellu, které nejsou `ModuleVersionOverrides` uvedeny v parametru, se aktualizují nejnovějšími verzemi modulů v Galerii PowerShellu. Pokud parametru `ModuleVersionOverrides` nic nepředáte, všechny moduly se aktualizují nejnovějšími verzemi modulů v galerii prostředí PowerShell. Toto chování je stejné jako **tlačítko Aktualizovat moduly Azure.**

## <a name="next-steps"></a>Další kroky

Další informace najdete v open source [modulech Update Azure runbook.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
