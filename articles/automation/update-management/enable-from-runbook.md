---
title: Povolit Azure Automation Update Management z Runbooku
description: V tomto článku se dozvíte, jak povolit Update Management ze sady Runbook.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: mvc
ms.openlocfilehash: 5377a3ff7ef7033b57f8785baa615a717ef7fa0f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575876"
---
# <a name="enable-update-management-from-a-runbook"></a>Povolení Update Managementu z runbooku

Tento článek popisuje, jak můžete pomocí Runbooku povolit funkci [Update Management](overview.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač s Update Management.

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](../how-to/region-mappings.md).

Tato metoda používá dva Runbooky:

* **Enable-MultipleSolution** – primární sada Runbook, která vyzve k zadání informací o konfiguraci, zadá dotaz na zadaný virtuální počítač a provede další kontroly ověřování a potom vyvolá sadu Runbook **Enable-AutomationSolution** pro konfiguraci Update Management pro každý virtuální počítač v zadané skupině prostředků.
* **Enable-AutomationSolution** – povolí Update Management pro jeden nebo více virtuálních počítačů zadaných v cílové skupině prostředků. Ověří splnění požadavků, ověří, že je nainstalované rozšíření virtuálního počítače Log Analytics a když se nenalezne, nainstaluje a přidá virtuální počítače do konfigurace oboru v zadaném Log Analytics pracovním prostoru, který je propojený s účtem Automation.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](../automation-security-overview.md) pro správu počítačů.
* [Pracovní prostor Log Analytics](../../azure-monitor/logs/design-logs-deployment.md)
* [Virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).
* Dva prostředky služby Automation, které jsou používány v sadě Runbook **Enable-AutomationSolution** . Tato sada Runbook, pokud už ve vašem účtu Automation neexistuje, je během prvního spuštění automaticky naimportována pomocí Runbooku **Enable-MultipleSolution** .
    * *LASolutionSubscriptionId*: ID předplatného, kde se nachází Log Analytics pracovní prostor.
    * *LASolutionWorkspaceId*: ID pracovního prostoru Log Analytics pracovního prostoru, který je propojený s vaším účtem Automation.

    Tyto proměnné se používají ke konfiguraci pracovního prostoru integrovaných virtuálních počítačů a je potřeba je vytvořit ručně. Pokud nejsou zadány, skript nejprve vyhledá libovolný virtuální počítač, který se Update Management v jeho předplatném a následuje předplatné, ve kterém je účet Automation, následovaný všemi ostatními předplatnými, ke kterým má váš uživatelský účet přístup. Pokud není správně nakonfigurovaný, může to mít za následek to, že se vaše počítače připojí do některého náhodného Log Analytics pracovního prostoru.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Povolení řešení Update Management

1. V Azure Portal přejděte na **účty Automation**. Na stránce **účty Automation** v seznamu vyberte svůj účet.

2. Ve svém účtu Automation v části **Update Management** vyberte **Update Management** .

3. Vyberte pracovní prostor Log Analytics a pak klikněte na **Povolit**. Když je povolená Update Management, zobrazí se banner.

    ![Povolení řešení Update Management](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>Instalace a aktualizace modulů

Je potřeba, abyste aktualizovali na nejnovější moduly Azure a importovali modul [AzureRM. OperationalInsights](/powershell/module/azurerm.operationalinsights) , abyste mohli úspěšně povolit Update Management pro virtuální počítače pomocí Runbooku.

1. Ve svém účtu Automation v části **sdílené prostředky** vyberte **moduly** .

2. Vyberte **Aktualizovat moduly Azure** a aktualizujte moduly Azure na nejnovější verzi.

3. Kliknutím na **Ano** aktualizujete všechny existující moduly Azure na nejnovější verzi.

    ![Aktualizace modulů](media/enable-from-runbook/update-modules.png)

4. Vraťte se do **modulů** v části **sdílené prostředky**.

5. Vyberte **Procházet galerii** a otevřete galerii modulů.

6. Vyhledejte `AzureRM.OperationalInsights` Tento modul a naimportujte ho do svého účtu Automation.

    ![Import modulu OperationalInsights](media/enable-from-runbook/import-operational-insights-module-azurerm.png)

## <a name="select-azure-vm-to-manage"></a>Vyberte virtuální počítač Azure, který se má spravovat.

Když je povoleno Update Management, můžete přidat virtuální počítač Azure pro příjem aktualizací.

1. Z účtu Automation v části **Správa aktualizací** vyberte **Správa aktualizací** .

2. Vyberte **Přidat virtuální počítače Azure** a přidejte svůj virtuální počítač.

3. Vyberte virtuální počítač ze seznamu a kliknutím na **Povolit** nakonfigurujte virtuální počítač pro správu.

   ![Povolit Update Management pro virtuální počítač](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > Pokud se pokusíte povolit další funkci před dokončením instalace Update Management, zobrazí se tato zpráva: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>Import Runbooku pro povolení Update Management

1. Ve svém účtu Automation v části **Automatizace procesu** vyberte **Runbooky** .

2. Vyberte **Procházet galerii**.

3. Vyhledejte **aktualizace a sledování změn**.

4. Vyberte Runbook a klikněte na tlačítko **importovat** na stránce **zdroj zobrazení** .

5. Kliknutím na **OK** importujte Runbook do účtu Automation.

   ![Importovat Runbook pro instalaci](media/enable-from-runbook/import-from-gallery.png)

6. Na stránce **sada Runbook** vyberte sadu Runbook **Enable-MultipleSolution** a pak klikněte na tlačítko **Upravit**. V textovém editoru vyberte  **publikovat**.

7. Po zobrazení výzvy k potvrzení klikněte na **Ano** , aby se Runbook publikoval.

## <a name="start-the-runbook"></a>Spuštění runbooku

Abyste mohli spustit tuto sadu Runbook, musíte mít povolený Update Management pro virtuální počítač Azure. Pro konfiguraci jednoho nebo více virtuálních počítačů v cílové skupině prostředků vyžaduje existující virtuální počítač a skupinu prostředků s povolenou funkcí.

1. Otevřete Runbook **Enable-MultipleSolution** .

   ![Vícenásobná sada Runbook řešení](media/enable-from-runbook/runbook-overview.png)

2. Klikněte na tlačítko Start a zadejte hodnoty parametrů do následujících polí:

   * **VMName** – název existujícího virtuálního počítače, který se má přidat do Update Management. Pokud chcete přidat všechny virtuální počítače ve skupině prostředků, ponechte toto pole prázdné.
   * **VMRESOURCEGROUP** – název skupiny prostředků pro virtuální počítače, které se mají povolit.
   * **SUBSCRIPTIONID** – ID předplatného nového virtuálního počítače, který se má povolit Pokud chcete použít předplatné pracovního prostoru, ponechte toto pole prázdné. Pokud používáte jiné ID předplatného, přidejte účet Spustit jako pro účet Automation jako přispěvatele pro předplatné.
   * **ALREADYONBOARDEDVM** – název virtuálního počítače, který je už ručně povolený pro aktualizace.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – název skupiny prostředků, do které virtuální počítač patří.
   * **SOLUTIONTYPE** – zadejte **aktualizace**.

   ![Parametry runbooku Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Vyberte **OK** a spusťte úlohu runbooku.

4. Sledujte průběh úlohy Runbooku a případné chyby ze stránky **úlohy** .

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro vaše virtuální](manage-updates-for-vm.md)počítače.

* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](../troubleshoot/update-management.md).
