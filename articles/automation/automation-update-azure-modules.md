---
title: Aktualizace Azure PowerShellch modulů v Azure Automation
description: Tento článek popisuje, jak teď můžete aktualizovat běžné Azure PowerShell moduly, které jsou ve výchozím nastavení k dispozici v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3f2ceda6e87ce16e910f3d215c1fabe81d522f32
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855560"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Aktualizace Azure PowerShellch modulů v Azure Automation

Pokud chcete aktualizovat moduly Azure v účtu Automation, musíte použít [sadu Runbook pro aktualizaci modulů Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), která je k dispozici jako open source. Pokud chcete začít používat sadu Runbook **Update-AutomationAzureModulesForAccount** , abyste mohli aktualizovat moduly Azure, Stáhněte si ho z části [Aktualizace Azure modules Runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) na GitHubu. Pak ho můžete importovat do svého účtu Automation nebo spustit jako skript. Informace o tom, jak importovat Runbook do svého účtu Automation, najdete v tématu [Import Runbooku](manage-runbooks.md#import-a-runbook).

Nejběžnější moduly PowerShellu jsou ve výchozím nastavení k dispozici v každém účtu Automation. Tým Azure pravidelně aktualizuje moduly Azure. Proto pokud chcete, aby moduly v účtech Automation byly aktuální, měli byste použít sadu Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .

Vzhledem k tomu, že moduly jsou pravidelně aktualizovány skupinou produktů, můžou se v zahrnutých rutinách provádět změny. Tyto změny, například přejmenování parametru nebo zastaralost rutiny zcela, můžou negativně ovlivnit vaše Runbooky.

Chcete-li se vyhnout vlivu na vaše Runbooky a procesy, které automatizují, otestujte a ověřte před tím, než budete pokračovat. Pokud nemáte pro tento účel vyhrazený účet služby Automation, zvažte jeho vytvoření, abyste mohli otestovat mnoho různých scénářů během vývoje runbooků. Toto testování by mělo zahrnovat iterativní změny, jako je například aktualizace modulů prostředí PowerShell.

Pokud vaše skripty vyvíjíte místně, doporučujeme mít v místním počítači stejné verze modulu, které máte v účtu Automation při testování, abyste se ujistili, že dostanete stejné výsledky. Po ověření výsledků a provedení všech požadovaných změn můžete změny přesunout do produkčního prostředí.

> [!NOTE]
> Nový účet Automation nemusí obsahovat nejnovější moduly.

> [!NOTE]
> Nebudete moci odstranit globální moduly, což jsou moduly, které automatizaci poskytují.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Požadavky

V následující části jsou některé předpoklady, které je potřeba vzít v úvahu při aktualizaci modulů Azure pomocí tohoto článku:

* Sada Runbook popsaná v tomto článku podporuje ve výchozím nastavení aktualizace pro Azure, AzureRM a AZ Modules. Další informace o aktualizaci AZ. Automation modules s touto sadou Runbook najdete v [souboru Readme pro aktualizace modulů Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) v sadě Runbook. K dispozici jsou další důležité faktory, které je potřeba vzít v úvahu při použití ve vašem účtu Automation ke službě AZ Modules. Další informace najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md).

* Před zahájením této sady Runbook se ujistěte, že má váš účet Automation vytvořené [přihlašovací údaje účtu Spustit jako pro Azure](manage-runas-account.md) .

* Tento kód můžete použít jako regulární skript PowerShellu místo Runbooku: stačí se přihlásit k Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) a pak ho předat `-Login $false` skriptu.

* Pokud chcete tuto sadu Runbook použít v cloudech svrchovaného `AzEnvironment` prostředí, použijte parametr pro předání správného prostředí Runbooku.  Přijatelné hodnoty jsou AzureCloud (veřejný cloud Azure), AzureChinaCloud, AzureGermanCloud a AzureUSGovernment. Tyto hodnoty lze načíst pomocí `Get-AzEnvironment | select Name`. Pokud do této rutiny nepředáte hodnotu, sada Runbook se nastaví jako výchozí AzureCloud.

* Pokud chcete použít konkrétní verzi modulu Azure PowerShell místo nejnovějšího modulu, který je k dispozici v Galerie prostředí PowerShell, předejte tyto verze volitelnému `ModuleVersionOverrides` parametru Runbooku **Update-AutomationAzureModulesForAccount** . Příklady najdete v tématu sada Runbook [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) . Azure PowerShell moduly, které nejsou uvedené v `ModuleVersionOverrides` parametru, se aktualizují pomocí nejnovějších verzí modulu v Galerie prostředí PowerShell. Pokud do `ModuleVersionOverrides` parametru nepředáte žádnou hodnotu, všechny moduly se aktualizují s nejnovějšími verzemi modulu na galerie prostředí PowerShell. Toto chování je stejné jako u tlačítka **aktualizovat moduly Azure** .

## <a name="next-steps"></a>Další kroky

Další informace o této službě najdete v části open source [Update Runbook modules Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .
