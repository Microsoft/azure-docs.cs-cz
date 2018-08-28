---
title: Začlenění řešení Update Management a Change Tracking do Azure Automation
description: Zjistěte, jak začlenit řešení Update Management a Change Tracking do Azure Automation.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 5cf09753645d8238232e064af2ba1a301a2a7217
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "41918182"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Začlenění řešení Update Management a Change Tracking do Azure Automation

V tomto kurzu zjistíte, jak automaticky začlenit řešení Update Management, Change Tracking a Inventory pro virtuální počítače do Azure Automation:

> [!div class="checklist"]
> * Připojení virtuálního počítače Azure
> * Povolení řešení
> * Instalace a aktualizace modulů
> * Import runbooku připojování
> * Spuštění runbooku

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="onboard-an-azure-vm"></a>Připojení virtuálního počítače Azure

Počítače můžete připojit několika způsoby – můžete připojit řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), [z procházení více počítačů](automation-onboard-solutions-from-browse.md), [ze svého účtu Automation](automation-onboard-solutions-from-automation-account.md) nebo pomocí runbooku. Tento kurz vás provede povolením řešení Update Management prostřednictvím runbooku. Pokud chcete připojit virtuální počítače Azure ve velkém měřítku, stávající virtuální počítače se musí připojit s řešením Change Tracking nebo Update Management. V tomto kroku připojíte virtuální počítač s řešením Update Management a Change Tracking.

### <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Řešení Change Tracking a Inventory poskytují možnost na virtuálních počítačích [sledovat změny](automation-vm-change-tracking.md) a [inventář](automation-vm-inventory.md). V tomto kroku povolíte řešení na virtuálním počítači.

1. V nabídce vlevo vyberte **Účty Automation** a pak v seznamu vyberte svůj účet Automation.
1. V části **SPRÁVA KONFIGURACE** vyberte **Inventory**.
1. Vyberte stávající pracovní prostor Log Analytics nebo vytvořte nový. Klikněte na tlačítko **Povolit**.

![Začlenění řešení Update Management](media/automation-onboard-solutions/inventory-onboard.png)

Po oznámení dokončení začlenění řešení Change Tracking a Inventory klikněte v části **SPRÁVA KONFIGURACE** na **Update Management**.

### <a name="enable-update-management"></a>Povolení řešení Update Management

Řešení Update Management umožňuje spravovat aktualizace a opravy pro virtuální počítače Azure s Windows. Můžete vyhodnotit stav dostupných aktualizací, naplánovat instalaci požadovaných aktualizací a zkontrolovat výsledky nasazení za účelem ověření správného použití aktualizací ve virtuálních počítačích. V tomto kroku povolíte řešení pro svůj virtuální počítač.

1. Ve svém účtu Automation v části **SPRÁVA AKTUALIZACÍ** vyberte **Update Management**.
1. Vybraný pracovní prostor Log Analytics je stejný jako pracovní prostor použitý v předchozím kroku. Kliknutím na **Povolit** začleňte řešení Update Management.

![Začlenění řešení Update Management](media/automation-onboard-solutions/update-onboard.png)

V průběhu instalace řešení Update Management se zobrazí modrý banner. Po povolení řešení v části **SPRÁVA KONFIGURACE** vyberte **Change Tracking** a přejděte k dalšímu kroku.

### <a name="select-azure-vm-to-be-managed"></a>Výběr virtuálního počítače pro správu

Když jsou teď řešení povolená, můžete přidat virtuální počítač Azure, který se k těmto řešením připojí.

1. Ve svém účtu Automation na stránce **Change Tracking** vyberte **+ Přidat virtuální počítač Azure** a přidejte svůj virtuální počítač.

1. V seznamu vyberte svůj virtuální počítač a vyberte **Povolit**. Tato akce pro virtuální počítač povolí řešení Change Tracking a Inventory.

   ![Povolení řešení Update Management pro virtuální počítač](media/automation-onboard-solutions/enable-change-tracking.png)

1. Po oznámení dokončení připojování virtuálního počítače ve svém účtu Automation v části **SPRÁVA AKTUALIZACÍ** vyberte **Update Management**.

1. Vyberte **+ Přidat virtuální počítač Azure** a přidejte svůj virtuální počítač.

1. V seznamu vyberte svůj virtuální počítač a vyberte **Povolit**. Tato akce pro virtuální počítač povolí řešení Update Management.

   ![Povolení řešení Update Management pro virtuální počítač](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Pokud nechcete čekat na dokončení dalšího řešení, při povolování dalšího řešení se zobrazí tato zpráva: *Na tomto nebo jiném virtuálním počítači probíhá instalace jiného řešení. Po dokončení této instalace se aktivuje tlačítko Povolit a můžete vyžádat instalaci řešení na tomto virtuálním počítači.*

## <a name="install-and-update-modules"></a>Instalace a aktualizace modulů

Pro úspěšnou automatizaci připojování k řešení je potřeba provést aktualizaci na nejnovější moduly Azure a importovat modul `AzureRM.OperationalInsights`.

## <a name="update-azure-modules"></a>Aktualizace modulů Azure

Ve svém účtu Automation v části **SDÍLENÉ PROSTŘEDKY** vyberte **Moduly**. Vyberte **Aktualizovat moduly Azure** a aktualizujte moduly Azure na nejnovější verzi. Po zobrazení výzvy vyberte **Ano** a aktualizujte všechny existující moduly Azure na nejnovější verzi.

![Aktualizace modulů](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Instalace modulu AzureRM.OperationalInsights

Z **moduly** vyberte **procházet galerii** otevřete v galerii modulů. Vyhledejte modul AzureRM.OperationalInsights a importujte ho do účtu Automation.

![Import modulu OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Import runbooku připojování

1. Ve svém účtu Automation v části **AUTOMATIZACE PROCESŮ** vyberte **Runbooky**.
1. Vyberte **Procházet galerii**.
1. Vyhledejte řešení **Update Management a Change Tracking**, klikněte na runbook a na stránce **Zobrazit zdroj** vyberte **Importovat**. Vyberte **OK** a importujte runbook do účtu Automation.

  ![Import runbooku připojování](media/automation-onboard-solutions/import-from-gallery.png)

1. Na stránce **Runbook** vyberte **Upravit** a pak **Publikovat**. V dialogovém okně **Publikovat runbook** vyberte **Ano** a publikujte runbook.

## <a name="start-the-runbook"></a>Spuštění runbooku

Abyste tento runbook mohli spustit, musíte mít k virtuálnímu počítači Azure připojené řešení Change Tracking nebo Update Management. Runbook vyžaduje existující virtuální počítač a skupinu prostředků s připojeným řešením kvůli parametrům.

1. Otevřete runbook Enable-MultipleSolution.

   ![Několik runbooků řešení](media/automation-onboard-solutions/runbook-overview.png)

1. Klikněte na tlačítko Spustit a zadejte pro parametry následující hodnoty.

   * **VMNAME** – Ponechte prázdné. Název existujícího virtuálního počítače, který se má připojit k řešení Update Management nebo Change Tracking. Když tuto hodnotu necháte prázdnou, připojí se všechny virtuální počítače ve skupině prostředků.
   * **VMRESOURCEGROUP** – Název skupiny prostředků pro virtuální počítače, které se mají připojit.
   * **SUBSCRIPTIONID** – Ponechte prázdné. ID předplatného nového virtuálního počítače, který se má připojit. Když tuto hodnotu necháte prázdnou, použije se předplatné pracovního prostoru. Když zadáte ID jiného předplatného, měli byste také přidat účet Spustit jako pro tento účet Automation jako přispěvatele tohoto předplatného.
   * **ALREADYONBOARDEDVM** – Název virtuálního počítače ručně připojeného k řešení Update Management nebo Change Tracking.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – Název skupiny prostředků, které je virtuální počítač členem.
   * **SOLUTIONTYPE** – Zadejte **Updates** nebo **ChangeTracking**

   ![Parametry runbooku Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Vyberte **OK** a spusťte úlohu runbooku.
1. Průběh a případné chyby můžete monitorovat na stránce úlohy runbooku.

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
