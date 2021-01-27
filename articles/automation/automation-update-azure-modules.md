---
title: Aktualizace Azure PowerShellch modulů v Azure Automation
description: Tento článek obsahuje informace o tom, jak aktualizovat běžné Azure PowerShell moduly, které jsou ve výchozím nastavení dostupné v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 413293cdefd39285c57b58d4555d3d703492ea96
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894843"
---
# <a name="update-azure-powershell-modules"></a>Aktualizace modulů Azure PowerShellu

Nejběžnější moduly PowerShellu jsou ve výchozím nastavení k dispozici v každém účtu Automation. Viz [výchozí moduly](shared-resources/modules.md#default-modules). Jelikož tým Azure aktualizuje moduly Azure pravidelně, můžou se v zahrnutých rutinách objevit změny. Tyto změny, například přejmenování parametru nebo zastaralost rutiny zcela, můžou negativně ovlivnit vaše Runbooky. 

> [!NOTE]
> Nemůžete odstranit globální moduly, což jsou moduly, které automatizaci poskytují.

## <a name="set-up-an-automation-account"></a>Nastavení účtu Automation

Chcete-li se vyhnout vlivu na vaše Runbooky a procesy, které automatizují, proveďte testování a ověření při provádění aktualizací. Pokud nemáte pro tento účel vyhrazený účet služby Automation, zvažte jeho vytvoření, abyste mohli otestovat mnoho různých scénářů během vývoje runbooků. Toto testování by mělo zahrnovat iterativní změny, jako je například aktualizace modulů prostředí PowerShell.

Ujistěte se, že váš účet Automation má vytvořené [přihlašovací údaje účtu Spustit jako pro Azure](manage-runas-account.md) .

Pokud vaše skripty vyvíjíte místně, doporučujeme mít v místním počítači stejné verze modulu, které máte v účtu Automation při testování, abyste měli jistotu, že dostanete stejné výsledky. Po ověření výsledků a provedení všech požadovaných změn můžete změny přesunout do produkčního prostředí.

> [!NOTE]
> Nový účet Automation nemusí obsahovat nejnovější moduly.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Získání Runbooku pro použití s aktualizacemi

Pokud chcete aktualizovat moduly Azure v účtu Automation, musíte použít sadu Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) , která je k dispozici jako open source. Pokud chcete začít s použitím této sady Runbook aktualizovat moduly Azure, Stáhněte si je z úložiště GitHub. Pak ho můžete importovat do svého účtu Automation nebo spustit jako skript. Informace o tom, jak importovat Runbook do svého účtu Automation, najdete v tématu [Import Runbooku](manage-runbooks.md#import-a-runbook).

Sada Runbook **Update-AutomationAzureModulesForAccount** podporuje ve výchozím nastavení aktualizovat moduly Azure, AzureRM a AZ. Další informace o aktualizaci AZ. Automation modules s touto sadou Runbook najdete v [souboru Readme pro aktualizace modulů Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) v sadě Runbook. K dispozici jsou další důležité faktory, které je potřeba vzít v úvahu při použití ve vašem účtu Automation ke službě AZ Modules. Další informace najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Použití aktualizace kódu Runbooku jako regulárního skriptu PowerShellu

Kód Runbooku můžete použít jako regulární skript PowerShellu namísto Runbooku. Pokud to chcete provést, přihlaste se k Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a pak ji předejte `-Login $false` do skriptu.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Použití sady Runbook aktualizace na cloudech z svrchovaného počítače

Chcete-li použít tuto sadu Runbook v rámci svrchovaného cloudu, použijte `AzEnvironment` parametr pro předání správného prostředí Runbooku. Přijatelné hodnoty jsou AzureCloud (veřejný cloud Azure), AzureChinaCloud, AzureGermanCloud a AzureUSGovernment. Tyto hodnoty lze načíst pomocí `Get-AzEnvironment | select Name` . Pokud do této rutiny nepředáte hodnotu, sada Runbook se nastaví jako výchozí AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Aktualizace konkrétní verze modulu pomocí Runbooku aktualizace

Pokud chcete použít konkrétní verzi modulu Azure PowerShell místo nejnovějšího modulu, který je k dispozici v Galerie prostředí PowerShell, předejte tyto verze volitelnému `ModuleVersionOverrides` parametru Runbooku **Update-AutomationAzureModulesForAccount** . Příklady najdete v tématu  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) Runbook. Azure PowerShell moduly, které nejsou uvedené v `ModuleVersionOverrides` parametru, se aktualizují pomocí nejnovějších verzí modulu v Galerie prostředí PowerShell. Pokud do parametru nepředáte žádnou hodnotu `ModuleVersionOverrides` , všechny moduly se aktualizují s nejnovějšími verzemi modulu na galerie prostředí PowerShell. Toto chování je stejné jako u tlačítka **aktualizovat moduly Azure** v Azure Portal.

## <a name="next-steps"></a>Další kroky

* Podrobnosti o používání modulů najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md).
* Informace o sadě Runbook aktualizace najdete v tématu [aktualizace Runbooku modulů Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
