---
title: Povolit Azure Automation Change Tracking a inventář z Runbooku
description: V tomto článku se dozvíte, jak povolit Change Tracking a inventář z Runbooku.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "100585931"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Povolení řešení Change Tracking a Inventory z runbooku

Tento článek popisuje, jak můžete pomocí Runbooku povolit [Change Tracking a inventář](overview.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Change Tracking a inventáře.

> [!NOTE]
> Při povolování Change Tracking a inventáře jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

Tato metoda používá dva Runbooky:

* **Enable-MultipleSolution** – primární Runbook, který vyzývá k informacím o konfiguraci, zadá dotaz na zadaný virtuální počítač a provede další kontroly ověřování a potom vyvolá sadu Runbook **Enable-AutomationSolution** pro konfiguraci Change Tracking a inventáře pro každý virtuální počítač v zadané skupině prostředků.
* **Enable-AutomationSolution** – povolí Change Tracking a inventář pro jeden nebo více virtuálních počítačů zadaných v cílové skupině prostředků. Ověří splnění požadavků, ověří, že je nainstalované rozšíření virtuálního počítače Log Analytics a když se nenalezne, nainstaluje a přidá virtuální počítače do konfigurace oboru v zadaném Log Analytics pracovním prostoru, který je propojený s účtem Automation.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../automation-security-overview.md) pro správu počítačů.
* [Pracovní prostor Log Analytics](../../azure-monitor/logs/design-logs-deployment.md)
* [Virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).
* Dva prostředky služby Automation, které jsou používány v sadě Runbook **Enable-AutomationSolution** . Tato sada Runbook, pokud už ve vašem účtu Automation neexistuje, je během prvního spuštění automaticky naimportována pomocí Runbooku **Enable-MultipleSolution** .
    * *LASolutionSubscriptionId*: ID předplatného, kde se nachází Log Analytics pracovní prostor.
    * *LASolutionWorkspaceId*: ID pracovního prostoru Log Analytics pracovního prostoru, který je propojený s vaším účtem Automation.

    Tyto proměnné se používají ke konfiguraci pracovního prostoru integrovaných virtuálních počítačů. Pokud nejsou zadány, skript nejprve vyhledá libovolný virtuální počítač, který je připojen k Change Tracking a inventarizaci v rámci svého předplatného, následované předplatným, na kterém je účet Automation, a za ním všechna ostatní předplatná, ke kterým má váš uživatelský účet přístup. Pokud není správně nakonfigurovaný, může to mít za následek to, že se vaše počítače připojí do některého náhodného Log Analytics pracovního prostoru.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

1. V Azure Portal přejděte na **účty Automation**. Na stránce **účty Automation** v seznamu vyberte svůj účet.

1. Ve svém účtu Automation v části **Správa konfigurace** vyberte **inventarizace** nebo **Change Tracking** .

1. Vyberte pracovní prostor Log Analytics a pak klikněte na **Povolit**. Při povolování inventáře nebo Change Tracking se zobrazí banner.

    ![Povolení řešení Change Tracking a Inventory](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Instalace a aktualizace modulů

Je potřeba, abyste aktualizovali na nejnovější moduly Azure a importovali modul [AZ. OperationalInsights](/powershell/module/az.operationalinsights) , který umožňuje úspěšně povolit Update Management pro vaše virtuální počítače pomocí Runbooku.

1. Ve svém účtu Automation v části **sdílené prostředky** vyberte **moduly** .

2. Vyberte **Aktualizovat moduly Azure** a aktualizujte moduly Azure na nejnovější verzi.

3. Kliknutím na **Ano** aktualizujete všechny existující moduly Azure na nejnovější verzi.

    ![Aktualizace modulů](media/enable-from-runbook/update-modules.png)

4. Vraťte se do **modulů** v části **sdílené prostředky**.

5. Vyberte **Procházet galerii** a otevřete galerii modulů.

6. Vyhledejte `Az.OperationalInsights` Tento modul a naimportujte ho do svého účtu Automation.

    ![Import modulu OperationalInsights](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Vyberte virtuální počítač Azure, který se má spravovat.

Po povolení Change Tracking a inventáře můžete přidat virtuální počítač Azure pro správu pomocí funkce.

1. Ve svém účtu Automation v části **Správa konfigurace** vyberte možnost **sledování změn** nebo **inventarizace** .

2. Kliknutím na **Přidat virtuální počítače Azure** přidejte svůj virtuální počítač.

3. V seznamu vyberte svůj virtuální počítač a klikněte na **Povolit**. Tato akce povolí pro virtuální počítač Change Tracking a inventář.

   ![Povolení Change Tracking a inventáře pro virtuální počítač](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Pokud se pokusíte povolit další funkci před instalací Change Tracking a inventáře, zobrazí se tato zpráva: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Import Runbooku pro povolení Change Tracking a inventáře

1. Ve svém účtu Automation v části **Automatizace procesu** vyberte **Runbooky** .

2. Vyberte **Procházet galerii**.

3. Vyhledejte **aktualizace a sledování změn**.

4. Vyberte Runbook a klikněte na tlačítko **importovat** na stránce **zdroj zobrazení** .

5. Kliknutím na **OK** importujte Runbook do účtu Automation.

   ![Importovat Runbook pro instalaci](media/enable-from-runbook/import-from-gallery.png)

6. Na stránce **sada Runbook** vyberte sadu Runbook **Enable-MultipleSolution** a pak klikněte na tlačítko **Upravit**. V textovém editoru vyberte  **publikovat**.

7. Po zobrazení výzvy k potvrzení klikněte na **Ano** , aby se Runbook publikoval.

## <a name="start-the-runbook"></a>Spuštění runbooku

Abyste mohli spustit tuto sadu Runbook, musíte mít povolený Change Tracking a inventář pro virtuální počítač Azure. Pro konfiguraci jednoho nebo více virtuálních počítačů v cílové skupině prostředků vyžaduje existující virtuální počítač a skupinu prostředků s povolenou funkcí.

1. Otevřete Runbook **Enable-MultipleSolution** .

   ![Vícenásobná sada Runbook řešení](media/enable-from-runbook/runbook-overview.png)

2. Klikněte na tlačítko Start a zadejte hodnoty parametrů do následujících polí:

   * **VMName** – název existujícího virtuálního počítače, který se má přidat do Change Tracking a inventáře. Pokud chcete přidat všechny virtuální počítače ve skupině prostředků, ponechte toto pole prázdné.
   * **VMRESOURCEGROUP** – název skupiny prostředků pro virtuální počítače, které se mají povolit.
   * **SUBSCRIPTIONID** – ID předplatného nového virtuálního počítače, který se má povolit Pokud chcete použít předplatné pracovního prostoru, ponechte toto pole prázdné. Pokud používáte jiné ID předplatného, přidejte účet Spustit jako pro účet Automation jako přispěvatele pro předplatné.
   * **ALREADYONBOARDEDVM** – název virtuálního počítače, který je už ručně povolený pro aktualizace.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – název skupiny prostředků, do které virtuální počítač patří.
   * **SOLUTIONTYPE** – zadejte **sledování změn ve**.

   ![Parametry runbooku Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Vyberte **OK** a spusťte úlohu runbooku.

4. Sledujte průběh úlohy Runbooku a případné chyby ze stránky **úlohy** .

## <a name="next-steps"></a>Další kroky

* Chcete-li naplánovat sadu Runbook, přečtěte si téma [Správa plánů v Azure Automation](../shared-resources/schedules.md).

* Podrobnosti o práci s funkcí najdete v tématu [správa Change Tracking](manage-change-tracking.md) a [Správa inventáře](manage-inventory-vms.md).

* Řešení obecných problémů s funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](../troubleshoot/change-tracking.md).


