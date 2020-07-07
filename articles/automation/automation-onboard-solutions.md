---
title: Povolit Azure Automation Update Management z Runbooku
description: V tomto článku se dozvíte, jak povolit Update Management ze sady Runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 95370a45b9fce69e5c1d06ac9bb987e1bfcfba4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204068"
---
# <a name="enable-update-management-from-a-runbook"></a>Povolení Update Managementu z runbooku

Tento článek popisuje, jak můžete pomocí Runbooku povolit funkci [Update Management](automation-update-management.md) pro virtuální počítače ve vašem prostředí. Pokud chcete povolit virtuální počítače Azure ve velkém měřítku, musíte povolit existující virtuální počítač pomocí Update Management. 

> [!NOTE]
> Při povolování Update Management jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a účtu Automation. Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md) pro správu počítačů.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-update-management"></a>Povolení řešení Update Management

1. Ve svém účtu Automation v části **Update Management**vyberte **Update Management** .

2. Vyberte pracovní prostor Log Analytics a pak klikněte na **Povolit**. I když je povolená Update Management, zobrazí se modrý banner. 

    ![Povolení řešení Update Management](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Vyberte virtuální počítač Azure, který se má spravovat.

Když je povoleno Update Management, můžete přidat virtuální počítač Azure pro příjem aktualizací.

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Vyberte **Přidat virtuální počítače Azure** a přidejte svůj virtuální počítač.

3. Vyberte virtuální počítač ze seznamu a kliknutím na **Povolit** nastavte virtuální počítač pro aktualizace. 

   ![Povolit Update Management pro virtuální počítač](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Pokud se pokusíte povolit další funkci před dokončením instalace Update Management, zobrazí se tato zpráva:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalace a aktualizace modulů

Je potřeba, abyste aktualizovali na nejnovější moduly Azure a importovali modul [AZ. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) , který umožňuje úspěšně povolit Update Management pro vaše virtuální počítače.

1. Ve svém účtu Automation v části **sdílené prostředky**vyberte **moduly** . 
2. Vyberte **Aktualizovat moduly Azure** a aktualizujte moduly Azure na nejnovější verzi. 
3. Kliknutím na **Ano** aktualizujete všechny existující moduly Azure na nejnovější verzi.

    ![Aktualizace modulů](media/automation-onboard-solutions/update-modules.png)

4. Vraťte se do **modulů** v části **sdílené prostředky**. 
5. Vyberte **Procházet galerii** a otevřete galerii modulů. 
6. Vyhledejte `Az.OperationalInsights` Tento modul a naimportujte ho do svého účtu Automation.

    ![Import modulu OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Import Runbooku pro povolení Update Management

1. Ve svém účtu Automation v části **Automatizace procesu**vyberte **Runbooky** .
2. Vyberte **Procházet galerii**.
3. Vyhledejte `update and change tracking`.
4. Vyberte Runbook a klikněte na tlačítko **importovat** na stránce zdroj zobrazení. 
5. Kliknutím na **OK** importujte Runbook do účtu Automation.

   ![Importovat Runbook pro instalaci](media/automation-onboard-solutions/import-from-gallery.png)

6. Na stránce sada Runbook klikněte na tlačítko **Upravit**a pak vyberte možnost **publikovat**. 
7. V podokně Publikovat sadu Runbook kliknutím na tlačítko **Ano** publikujte sadu Runbook.

## <a name="start-the-runbook"></a>Spuštění runbooku

Abyste mohli spustit tuto sadu Runbook, musíte mít povolený Update Management pro virtuální počítač Azure. Vyžaduje existující virtuální počítač a skupinu prostředků s povolenou funkcí pro parametry.

1. Otevřete Runbook **Enable-MultipleSolution** .

   ![Vícenásobná sada Runbook řešení](media/automation-onboard-solutions/runbook-overview.png)

2. Klikněte na tlačítko Start a zadejte hodnoty parametrů do následujících polí:

   * **VMName** – název existujícího virtuálního počítače, který se má přidat do Update Management. Pokud chcete přidat všechny virtuální počítače ve skupině prostředků, ponechte toto pole prázdné.
   * **VMRESOURCEGROUP** – název skupiny prostředků pro virtuální počítače, které se mají povolit.
   * **SUBSCRIPTIONID** – ID předplatného nového virtuálního počítače, který se má povolit Pokud chcete použít předplatné pracovního prostoru, ponechte toto pole prázdné. Pokud používáte jiné ID předplatného, přidejte účet Spustit jako pro účet Automation jako přispěvatele pro předplatné.
   * **ALREADYONBOARDEDVM** – název virtuálního počítače, který je už ručně povolený pro aktualizace.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – název skupiny prostředků, do které virtuální počítač patří.
   * **SOLUTIONTYPE** – zadejte **aktualizace**.

   ![Parametry runbooku Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

3. Vyberte **OK** a spusťte úlohu runbooku.
4. Průběh a případné chyby můžete monitorovat na stránce úlohy runbooku.

## <a name="next-steps"></a>Další kroky

* Chcete-li naplánovat sadu Runbook, přečtěte si téma [Správa plánů v Azure Automation](shared-resources/schedules.md).
* Pokud chcete použít Update Management pro virtuální počítače, přečtěte si téma [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
* Pokud chcete řešit obecné chyby Update Management, přečtěte si téma [řešení potíží s Update Management](troubleshoot/update-management.md).
* Informace o řešení problémů s agentem Windows Update najdete v tématu řešení potíží s [agentem pro Windows Update](troubleshoot/update-agent-issues.md).
* Informace o řešení problémů s agentem aktualizací pro Linux najdete v tématu [řešení potíží s agentem aktualizace pro Linux](troubleshoot/update-agent-issues-linux.md).