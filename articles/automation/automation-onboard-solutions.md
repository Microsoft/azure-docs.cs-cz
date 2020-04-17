---
title: Řešení pro aktualizace, sledování změn a inventář e-do Azure Automation
description: Zjistěte, jak začlenit řešení Update Management a Change Tracking do Azure Automation.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457616"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Řešení pro aktualizace, sledování změn a inventář e-do Azure Automation

V tomto kurzu zjistíte, jak automaticky začlenit řešení Update Management, Change Tracking a Inventory pro virtuální počítače do Azure Automation:

> [!div class="checklist"]
> * Připojení virtuálního počítače Azure
> * Povolení řešení
> * Instalace a aktualizace modulů
> * Import runbooku připojování
> * Spuštění runbooku

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="onboard-an-azure-vm"></a>Připojení virtuálního počítače Azure

Počítače můžete připojit několika způsoby – můžete připojit řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), [z procházení více počítačů, ](automation-onboard-solutions-from-browse.md) [ze svého účtu Automation](automation-onboard-solutions-from-automation-account.md) nebo pomocí runbooku. Tento kurz vás provede povolením řešení Update Management prostřednictvím runbooku. Pokud chcete připojit virtuální počítače Azure ve velkém měřítku, stávající virtuální počítače se musí připojit s řešením Change Tracking nebo Update Management. V tomto kroku připojíte virtuální počítač s řešením Update Management a Change Tracking.

### <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Řešení sledování změn a inventáře umožňují [sledovat změny](automation-vm-change-tracking.md) a [inventář](automation-vm-inventory.md) na virtuálních počítačích. V tomto kroku povolíte řešení na virtuálním počítači.

1. Na webu Azure Portal vyberte **Účty automatizace**a v seznamu vyberte účet automatizace.
1. V části **Správa konfigurace**vyberte Možnost **Zásoby** .
1. Vyberte existující pracovní prostor Log Analytics nebo vytvořte nový. 
1. Klepněte na tlačítko **Povolit**.

    ![Začlenění řešení Update Management](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Povolení řešení Update Management

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače Azure s Windows. Můžete vyhodnotit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení za účelem ověření správného použití aktualizací ve virtuálních počítačích. V tomto kroku povolíte řešení pro svůj virtuální počítač.

1. V účtu Automation vyberte v části **Správa aktualizací** položku Správa **aktualizací.**
1. Vybraný pracovní prostor Analýzy protokolů je pracovní prostor použitý v předchozím kroku. Kliknutím na **Povolit** začleňte řešení Update Management. V průběhu instalace řešení Update Management se zobrazí modrý banner. 

    ![Začlenění řešení Update Management](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Výběr virtuálního počítače pro správu

Když jsou teď řešení povolená, můžete přidat virtuální počítač Azure, který se k těmto řešením připojí.

1. V části Správa konfigurace vyberte v části **Správa konfigurace**možnost **Sledování změn** . 
2. Na stránce Sledování změn klikněte na **Přidat virtuální počítače Azure** a přidejte virtuální počítač.

3. Vyberte virtuální počítač ze seznamu a klikněte na **Povolit**. Tato akce umožňuje řešení sledování změn a zásob pro virtuální ho.

   ![Povolení řešení Update Management pro virtuální počítač](media/automation-onboard-solutions/enable-change-tracking.png)

4. Po dokončení oznámení o připojení virtuálního počítači vyberte v části **Správa aktualizací** **možnost Správa aktualizací** .

5. Vyberte **Přidat virtuální počítače Azure** a přidejte virtuální počítač.

6. V seznamu vyberte svůj virtuální počítač a vyberte **Povolit**. Tato akce umožňuje řešení správy aktualizací pro virtuální ho.

   ![Povolení řešení Update Management pro virtuální počítač](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Pokud nečekáte na dokončení jiného řešení, při povolení dalšího řešení se zobrazí zpráva:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalace a aktualizace modulů

Je nutné aktualizovat na nejnovější moduly Azure a importovat modul [Az.OperationalInsights,](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) aby bylo možné úspěšně automatizovat přiregistraci řešení.

## <a name="update-azure-modules"></a>Aktualizace modulů Azure

1. V účtu Automation vyberte **moduly v** části **Sdílené prostředky**. 
2. Vyberte **Aktualizovat moduly Azure** a aktualizujte moduly Azure na nejnovější verzi. 
3. Kliknutím na **Ano** aktualizujte všechny existující moduly Azure na nejnovější verzi.

![Aktualizační](media/automation-onboard-solutions/update-modules.png) moduly A

### <a name="install-azoperationalinsights-module"></a>Instalace modulu Az.OperationalInsights

1. V účtu Automatizace vyberte **moduly v** části **Sdílené prostředky**. 
2. Vyberte **Galerie procházení** a otevřete galerii modulů. 
3. Vyhledejte Az.OperationalInsights a importujte tento modul do účtu Automatizace.

![Import modulu OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Import runbooku připojování

1. V účtu Automation vyberte **runbooky** v části **Automatizace procesů**.
1. Vyberte **Procházet galerii**.
1. Vyhledejte `update and change tracking`.
3. Vyberte runbook a na stránce Zdroj zobrazení klikněte na **Importovat.** 
4. Klepnutím na **tlačítko OK** importujte runbook do účtu Automation.

   ![Import runbooku připojování](media/automation-onboard-solutions/import-from-gallery.png)

6. Na stránce Runbook klikněte na **Edit**, a pak vyberte **Publikovat**. 
7. V podokně Publikovat runbook klikněte na **Ano** a publikujte ji.

## <a name="start-the-runbook"></a>Spuštění runbooku

Chcete-li spustit tuto runbook, musíte mít na palubě buď řešení pro sledování změn, nebo aktualizovat řešení pro virtuální počítač Azure. Runbook vyžaduje existující virtuální počítač a skupinu prostředků s připojeným řešením kvůli parametrům.

1. Otevřete runbook **Enable-MultipleSolution.**

   ![Několik runbooků řešení](media/automation-onboard-solutions/runbook-overview.png)

1. Klikněte na tlačítko Spustit a zadejte pro parametry následující hodnoty.

   * **VMNAME** – Ponechte prázdné. Název existujícího virtuálního počítače, který se má připojit k řešení Update Management nebo Change Tracking. Když tuto hodnotu necháte prázdnou, připojí se všechny virtuální počítače ve skupině prostředků.
   * **VMRESOURCEGROUP** – Název skupiny prostředků pro virtuální počítače, které se mají připojit.
   * **SUBSCRIPTIONID** – Ponechte prázdné. ID předplatného nového virtuálního počítače, který se má připojit. Když tuto hodnotu necháte prázdnou, použije se předplatné pracovního prostoru. Když zadáte ID jiného předplatného, měli byste také přidat účet Spustit jako pro tento účet Automation jako přispěvatele tohoto předplatného.
   * **ALREADYONBOARDEDVM** – Název virtuálního počítače ručně připojeného k řešení Update Management nebo Change Tracking.
   * **ALREADYONEDVMRESOURCEGROUP** - Název skupiny prostředků, do které patří virtuální počítač.
   * **SOLUTIONTYPE** - Zadejte **aktualizace** nebo **ChangeTracking**.

   ![Parametry runbooku Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Vyberte **OK** a spusťte úlohu runbooku.
1. Průběh a případné chyby můžete monitorovat na stránce úlohy runbooku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání virtuálního virtuálního virtuálního montovazy ze správy aktualizací:

1. V pracovním prostoru Log Analytics odeberte virtuální počítač z `MicrosoftDefaultScopeConfig-Updates`uloženého hledání konfigurace oboru . Uložená hledání najdete v části **Obecné** ve vašem pracovním prostoru.
2. Odeberte [agenta Log Analytics pro Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [agenta Analýzy protokolů pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Ručně připojit virtuální počítač Azure.
> * Nainstalovat a aktualizovat požadované moduly Azure.
> * Importovat runbook, který připojí virtuální počítače Azure.
> * Spustit runbook, který automaticky připojí virtuální počítače Azure.

Další informace o plánování runbooků najdete na následujícím odkazu.

> [!div class="nextstepaction"]
> [Plánování runbooků](automation-schedules.md)
