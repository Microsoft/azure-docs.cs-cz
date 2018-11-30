---
title: Aktualizovat moduly Azure ve službě Azure Automation
description: Tento článek popisuje, jak můžete nyní aktualizovat běžných modulů Azure Powershellu, které jsou k dispozici ve výchozím nastavení ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fc605ab45241280d9331ad7d515ba007a015daa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583649"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Aktualizace modulů Azure Powershellu ve službě Azure Automation

Ve výchozím nastavení v jednotlivých účtů Automation jsou k dispozici většiny běžných modulů Azure Powershellu. Tým Azure pravidelně aktualizuje moduly Azure. Ve vašem účtu Automation se poskytuje způsob, jak aktualizovat moduly v účtu, když je nová verze dostupná z portálu.

Protože moduly jsou pravidelně aktualizovány produktovou skupinou, změny můžou nastat pomocí rutiny zahrnuté. Tato akce může mít negativní vliv na vaše sady runbook v závislosti na typ změny, jako například přejmenování parametru nebo zcela vyřazení rutiny. Aby se zabránilo dopadu na vaše sady runbook a procesy, automatizovat, testování a ověřování než budete pokračovat. Pokud nemáte určené pro tento účel vyhrazený účet služby Automation, zvažte vytvoření jednoho tak, aby můžete vyzkoušet řadu různých scénářů při vývoji vašich sadách runbook. Tento test by měl obsahovat iterativní změny, jako je aktualizace modulů prostředí PowerShell. Po výsledky se ověří a použili potřebné změny, můžete přesunout změny do produkčního prostředí.

> [!NOTE]
> Nový účet Automation nemusí obsahovat nejnovější moduly.

## <a name="updating-azure-modules"></a>Aktualizace modulů Azure

1. Na stránce modulů vašeho účtu Automation je možnost **aktualizace modulů Azure**. Je vždy povoleno.<br><br> ![Aktualizace modulů Azure možnost na stránce modulů](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Před aktualizací moduly Azure, se doporučuje je aktualizovat v rámci testu účtu Automation a zkontrolujte, že vaše stávající skripty fungovat podle očekávání před aktualizací moduly Azure.
  >
  > **Aktualizace modulů Azure** tlačítko je k dispozici pouze ve veřejném cloudu. není k dispozici v [suverénních oblastech](https://azure.microsoft.com/global-infrastructure/). Podrobnosti najdete na [alternativní způsoby, jak aktualizovat moduly](#alternative-ways-to-update-your-modules) naleznete další informace.


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

Můžete importovat a spustit [aktualizace AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) sady runbook se pokuste aktualizovat moduly Azure ve vašem účtu Automation. Tento proces může selhat, pokud je verze, které se pokoušíte importovat z Galerie nejsou kompatibilní se službami Azure, které jsou aktuálně nasazené do cílového prostředí Azure. To může vyžadovat Ujistěte se, že kompatibilní verze moduly jsou uvedeny v parametry runbooku.

Použití `AzureRmEnvironment` parametr předat správné prostředí do runbooku.  Přípustné hodnoty jsou **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, a **AzureUSGovernment**. Tyto hodnoty lze získat pomocí `Get-AzureRmEnvironment | select Name`. Pokud nemáte předat hodnotu tomuto parametru, sada runbook bude ve výchozím nastavení veřejného cloudu Azure **AzureCloud**

Pokud chcete použít konkrétní verzi modulu Azure Powershellu místo nejnovější dostupné v galerii prostředí PowerShell, předat tyto verze volitelného `ModuleVersionOverrides` parametr **aktualizace AzureModule** sady runbook. Příklady najdete v tématu [aktualizace AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) sady runbook. Moduly prostředí Azure PowerShell, které nejsou uvedené v `ModuleVersionOverrides` parametru jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Pokud předáte nic `ModuleVersionOverrides` parametr, všechny moduly jsou aktualizovány pomocí nejnovější verze modulu v galerii prostředí PowerShell. Toto chování je stejné jako **aktualizace modulů Azure** tlačítko.

## <a name="next-steps"></a>Další postup

* Další informace o integrační moduly a vytvoření vlastních modulů k další integraci s jinými systémy, služby nebo řešení automatizace, naleznete v tématu [moduly integrace](automation-integration-modules.md).

