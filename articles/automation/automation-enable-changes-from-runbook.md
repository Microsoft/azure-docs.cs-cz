---
title: Povolit Azure Automation Change Tracking a inventář z Runbooku
description: V tomto článku se dozvíte, jak povolit Change Tracking a inventář z Runbooku.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 92149289c6c422179ddb3562274020acf779f10b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186278"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Povolení řešení Change Tracking a Inventory z runbooku

Tento článek popisuje, jak můžete pomocí Runbooku povolit funkci [Change Tracking a inventáře](change-tracking.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Change Tracking a inventáře. 

> [!NOTE]
> Při povolování Change Tracking a inventáře jsou podporovány pouze určité oblasti pro propojení Log Analyticsho pracovního prostoru a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](./index.yml) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory 

1. V Azure Portal vyberte **účty Automation**a potom v seznamu vyberte svůj účet Automation.
1. V části **Správa konfigurace**vyberte **inventarizace** .
1. Vyberte existující pracovní prostor Log Analytics nebo vytvořte nový. 
1. Klikněte na **Povolit**.

    ![Povolení řešení Change Tracking a Inventory](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Vyberte virtuální počítač Azure, který se má spravovat.

Po povolení Change Tracking a inventáře můžete přidat virtuální počítač Azure pro správu pomocí funkce.

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte možnost **sledování změn** nebo **inventarizace** .

2. Kliknutím na **Přidat virtuální počítače Azure** přidejte svůj virtuální počítač.

3. V seznamu vyberte svůj virtuální počítač a klikněte na **Povolit**. Tato akce povolí pro virtuální počítač Change Tracking a inventář.

   ![Povolení Change Tracking a inventáře pro virtuální počítač](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Pokud se pokusíte povolit další funkci před instalací Change Tracking a inventáře, zobrazí se tato zpráva: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalace a aktualizace modulů

Je potřeba, abyste aktualizovali na nejnovější moduly Azure a importovali modul [AZ. OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) , který umožňuje úspěšně povolit Change Tracking a inventář pro váš virtuální počítač.

1. Ve svém účtu Automation v části **sdílené prostředky**vyberte **moduly** . 
2. Vyberte **Aktualizovat moduly Azure** a aktualizujte moduly Azure na nejnovější verzi. 
3. Kliknutím na **Ano** aktualizujete všechny existující moduly Azure na nejnovější verzi.

    ![Aktualizace modulů](media/automation-enable-changes-from-runbook/update-modules.png)

4. Vraťte se do **modulů** v části **sdílené prostředky**. 
5. Vyberte **Procházet galerii** a otevřete galerii modulů. 
6. Vyhledejte AZ. OperationalInsights a importujte tento modul do účtu Automation.

    ![Import modulu OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Import Runbooku pro povolení Change Tracking a inventáře

1. Ve svém účtu Automation v části **Automatizace procesu**vyberte **Runbooky** .
2. Vyberte **Procházet galerii**.
3. Vyhledejte `update and change tracking`.
4. Vyberte Runbook a klikněte na tlačítko **importovat** na stránce zdroj zobrazení. 
5. Kliknutím na **OK** importujte Runbook do účtu Automation.

   ![Importovat Runbook pro instalaci](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Na stránce sada Runbook klikněte na tlačítko **Upravit**a pak vyberte možnost **publikovat**. 
7. V podokně Publikovat sadu Runbook kliknutím na tlačítko **Ano** publikujte sadu Runbook.

## <a name="start-the-runbook"></a>Spuštění runbooku

Abyste mohli spustit tuto sadu Runbook, musíte mít povolený Change Tracking a inventář pro virtuální počítač Azure. Vyžaduje existující virtuální počítač a skupinu prostředků s povolenou funkcí pro parametry.

1. Otevřete Runbook **Enable-MultipleSolution** .

   ![Několik runbooků řešení](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Klikněte na tlačítko Start a zadejte hodnoty parametrů do následujících polí:

   * **VMName** – název existujícího virtuálního počítače, který se má přidat do Change Tracking a inventáře. Pokud chcete přidat všechny virtuální počítače ve skupině prostředků, ponechte toto pole prázdné.
   * **VMRESOURCEGROUP** – název skupiny prostředků pro virtuální počítače, které se mají povolit.
   * **SUBSCRIPTIONID** – ID předplatného nového virtuálního počítače, který se má povolit Pokud chcete použít předplatné pracovního prostoru, ponechte toto pole prázdné. Pokud používáte jiné ID předplatného, přidejte účet Spustit jako pro účet Automation jako přispěvatele pro předplatné.
   * **ALREADYONBOARDEDVM** – název virtuálního počítače, který je už ručně povolený pro změny.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – název skupiny prostředků, do které virtuální počítač patří.
   * **SOLUTIONTYPE** – zadejte **sledování změn ve**.

   ![Parametry runbooku Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Vyberte **OK** a spusťte úlohu runbooku.
1. Průběh a případné chyby můžete monitorovat na stránce úlohy runbooku.

## <a name="next-steps"></a>Další kroky

* Chcete-li naplánovat sadu Runbook, přečtěte si téma [Správa plánů v Azure Automation](shared-resources/schedules.md).
* Podrobnosti o práci s funkcí najdete v tématu [správa Change Tracking a inventáře](change-tracking-file-contents.md).
* Řešení obecných problémů s funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).
