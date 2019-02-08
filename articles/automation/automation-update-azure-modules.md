---
title: Aktualizovat moduly Azure ve službě Azure Automation
description: Tento článek popisuje, jak můžete nyní aktualizovat běžných modulů Azure Powershellu, které jsou k dispozici ve výchozím nastavení ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/06/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 637cf4b0e53055e114536e591b334d51d5ddcc92
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883895"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Aktualizace modulů Azure Powershellu ve službě Azure Automation

Aktualizovat moduly Azure ve vašem účtu Automation se doporučuje vám tentokrát použijte [runbooku Azure aktualizovat moduly](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), která je teď open source. Kromě toho můžete stále použít runbook pomocné rutiny [aktualizace AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) nebo použijte **aktualizovat moduly Azure** tlačítko Aktualizovat moduly Azure na portálu. Další informace o používání sady runbook open source, najdete v článku [aktualizace modulů Azure pomocí opensourcových sad runbook](#open-source).

Ve výchozím nastavení v jednotlivých účtů Automation jsou k dispozici většiny běžných modulů Azure Powershellu. Tým Azure pravidelně aktualizuje moduly Azure. Ve vašem účtu Automation se poskytuje způsob, jak aktualizovat moduly v účtu, když je nová verze dostupná z portálu.

> [!NOTE]
> Nové [modul Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azurermps-6.13.0) nejsou podporované ve službě Azure Automation.

Protože moduly jsou pravidelně aktualizovány produktovou skupinou, změny můžou nastat pomocí rutiny zahrnuté. Tato akce může mít negativní vliv na vaše sady runbook v závislosti na typ změny, jako například přejmenování parametru nebo zcela vyřazení rutiny.

Aby se zabránilo dopadu na vaše sady runbook a procesy, automatizovat, testování a ověřování než budete pokračovat. Pokud nemáte určené pro tento účel vyhrazený účet služby Automation, zvažte vytvoření jednoho tak, aby můžete vyzkoušet řadu různých scénářů při vývoji vašich sadách runbook. Tento test by měl obsahovat iterativní změny, jako je aktualizace modulů prostředí PowerShell. 

Pokud vyvíjíte skripty místně, doporučuje se mít stejnou verzí modulů místně, které máte ve svém účtu Automation při testování pro zajištění vám zobrazí stejné výsledky. Po výsledky se ověří a použili potřebné změny, můžete přesunout změny do produkčního prostředí.

> [!NOTE]
> Nový účet Automation nemusí obsahovat nejnovější moduly.

## <a name="open-source"></a>Aktualizace modulů Azure s open source sady runbook

Pokud chcete začít používat **aktualizace AutomationAzureModulesForAccount** stažení sady runbook, chcete-li aktualizovat moduly Azure z [Azure aktualizovat moduly sady runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) na Githubu. Potom můžete importovat do účtu služby Automation nebo spustit jako skript. Pokyny o tom, jak to udělat najdete v [aktualizace Azure moduly sady runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

### <a name="considerations"></a>Požadavky

Tady jsou některé aspekty, vezměte v úvahu při použití tohoto procesu se aktualizovat moduly Azure:

* Při importu této sady runbook s původním názvem `Update-AutomationAzureModulesForAccount`, přepíše interní sady runbook s tímto názvem. V důsledku toho, importované sady runbook se spustí, když **aktualizace modulů Azure** tlačítko je nebo po vyvolání Tato sada runbook přímo prostřednictvím rozhraní API Azure Resource Manageru pro tento účet Automation.

* Pouze `Azure` a `AzureRM.*` moduly jsou aktuálně podporovány. Nové [moduly Azure Powershellu Az](/powershell/azure/new-azureps-module-az) se zatím nepodporují.

* Vyhněte se spouští tento runbook na účty Automation, které obsahují Az moduly.

* Před spuštěním této sady runbook, ujistěte se, že má váš účet služby Automation [pověření účtu spustit jako pro Azure](manage-runas-account.md) vytvořili.

* Tento kód můžete použít jako regulární skript Powershellu místo sady runbook: stačí se přihlásit do Azure s využitím [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) první příkaz a pak předejte `-Login $false` do skriptu.

* Chcete-li použít tuto sadu runbook v suverénních cloudech, použijte `AzureRmEnvironment` parametr předat správné prostředí do runbooku.  Přípustné hodnoty jsou **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, a **AzureUSGovernment**. Tyto hodnoty lze získat pomocí `Get-AzureRmEnvironment | select Name`. Pokud nemáte předat hodnotu tomuto parametru, sada runbook bude ve výchozím nastavení veřejného cloudu Azure **AzureCloud**

* Pokud chcete použít konkrétní verzi modulu Azure Powershellu místo nejnovější dostupné v galerii prostředí PowerShell, předat tyto verze volitelného `ModuleVersionOverrides` parametr **Update-AutomationAzureModulesForAccount**sady runbook. Příklady najdete v tématu [aktualizace AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) sady runbook. Moduly prostředí Azure PowerShell, které nejsou uvedené v `ModuleVersionOverrides` parametru jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Pokud předáte nic `ModuleVersionOverrides` parametr, všechny moduly jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Toto chování je stejné jako **aktualizace modulů Azure** tlačítko.

## <a name="update-azure-modules-in-the-azure-portal"></a>Aktualizace modulů Azure na webu Azure Portal

1. Na stránce modulů vašeho účtu Automation je možnost **aktualizace modulů Azure**. Je vždy povoleno.<br><br> ![Aktualizace modulů Azure možnost na stránce modulů](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Před aktualizací moduly Azure, se doporučuje je aktualizovat v rámci testu účtu Automation a zkontrolujte, že vaše stávající skripty fungovat podle očekávání před aktualizací moduly Azure.
  >
  > **Aktualizace modulů Azure** tlačítko je k dispozici pouze ve veřejném cloudu. není k dispozici v [suverénních oblastech](https://azure.microsoft.com/global-infrastructure/). Použijte prosím **aktualizace AutomationAzureModulesForAccount** sady runbook, chcete-li aktualizovat moduly Azure. Můžete ji stáhnout [aktualizace Azure moduly sady runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). Další informace o použití opensourcových sad runbook najdete v tématu [aktualizace modulů Azure pomocí opensourcových sad runbook](#open-source).

2. Klikněte na tlačítko **aktualizace modulů Azure**, zobrazí potvrzení oznámení, která požádá, pokud chcete pokračovat.<br><br> ![Aktualizace modulů Azure oznámení](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Klikněte na tlačítko **Ano** a zahájí se proces aktualizace modulu. Proces aktualizace trvá asi 15 až 20 minut a aktualizujte následující moduly:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Pokud moduly jsou již aktuální, proces dokončí za pár sekund. Po dokončení procesu aktualizace, zobrazí se oznámení.<br><br> ![Aktualizovat stav aktualizace modulů Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    Moduly AzureRm .NET core (AzureRm.*. Core) nejsou podporované ve službě Azure Automation a nelze jej importovat.

> [!NOTE]
> Při spuštění novou naplánovanou úlohu Azure Automation používá nejnovější moduly ve vašem účtu Automation.  

Pokud používáte rutiny z těchto modulů Azure Powershellu ve vašich sadách runbook, chcete spustit tento proces aktualizace každý měsíc nebo proto ujistěte se, že máte nejnovější moduly. Pomocí služby Azure Automation `AzureRunAsConnection` připojení k ověření při aktualizaci modulů. Pokud instanční objekt je prošlý nebo už neexistuje na úrovni předplatného, modul aktualizace se nezdaří.

## <a name="alternative-ways-to-update-your-modules"></a>Alternativní způsoby, jak aktualizovat moduly

Jak je uvedeno, **aktualizace modulů Azure** tlačítko není dostupné v suverénních cloudech, je dostupná jenom v globální cloud Azure. Toto je skutečnost, že nejnovější verze modulů Azure Powershellu z Galerie prostředí PowerShell nemusí fungovat pomocí prostředků Resource Manageru, které jsou aktuálně nasazené v tyto cloudy.

Stále můžete importovat a spustit [aktualizace AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) sady runbook se pokuste aktualizovat moduly Azure ve vašem účtu Automation. Ale doporučuje se, že používáte **aktualizace AutomationAzureModulesForAccount** sady runbook, chcete-li aktualizovat moduly Azure. Můžete ji stáhnout [aktualizace Azure moduly sady runbook úložiště](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). Další informace o používání sady runbook open source, najdete v článku [aktualizace modulů Azure pomocí opensourcových sad runbook](#open-source).

Obecně je vhodné aktualizovat všechny moduly Azure ve stejnou dobu. Ale tento proces může selhat, pokud verze, které se pokoušíte importovat z galerie, nebudou kompatibilní se službami Azure, které jsou aktuálně nasazené do cílového prostředí Azure. To může vyžadovat ověření, že kompatibilní verze moduly jsou uvedeny v parametry runbooku.

Použití `AzureRmEnvironment` parametr předat správné prostředí do runbooku.  Přípustné hodnoty jsou **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, a **AzureUSGovernment**. Tyto hodnoty lze získat pomocí `Get-AzureRmEnvironment | select Name`. Pokud nemáte předat hodnotu tomuto parametru, sada runbook bude ve výchozím nastavení veřejného cloudu Azure **AzureCloud**

Pokud chcete použít konkrétní verzi modulu Azure Powershellu místo nejnovější dostupné v galerii prostředí PowerShell, předat tyto verze volitelného `ModuleVersionOverrides` parametr **aktualizace AzureModule** sady runbook. Příklady najdete v tématu [aktualizace AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) sady runbook. Moduly prostředí Azure PowerShell, které nejsou uvedené v `ModuleVersionOverrides` parametru jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Pokud předáte nic `ModuleVersionOverrides` parametr, všechny moduly jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Toto chování je stejné jako **aktualizace modulů Azure** tlačítko.

## <a name="next-steps"></a>Další postup

* Přejděte [runbooku moduly Azure aktualizace](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Další informace o tom.
