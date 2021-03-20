---
title: Integrace služby Azure Automation se službou Event Grid | Microsoft Docs
description: Zjistěte, jak automaticky přidat značku, když se vytvoří nový virtuální počítač, a odeslat oznámení do Microsoft Teams.
keywords: automation, runbook, teams, event grid, virtual machine, VM
services: automation,event-grid
author: eamonoreilly
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 07/07/2020
ms.author: eamono
ms.openlocfilehash: 3b9b49a4d38566891f442a3d2d7eac9bf1d36465
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461999"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Kurz: Integrace Azure Automation s Event Grid a Microsoft Teams

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importovat ukázkový runbook Event Gridu
> * Vytvořit volitelný webhook Microsoft Teams
> * Vytvořit webhook pro runbook
> * Vytvoří odběr Event Gridu.
> * Vytvořit virtuální počítač, který spustí runbook

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

K dokončení tohoto kurzu je potřeba [účet Azure Automation](../automation/index.yml) pro uložení runbooku, který se spouští z předplatného Azure Event Gridu.

* Modul `AzureRM.Tags` musí být načtený v účtu Automation. Způsob importu modulů do Azure Automation najdete v [popisu importu modulů do Azure Automation](../automation/automation-update-azure-modules.md).

## <a name="import-an-event-grid-sample-runbook"></a>Import ukázkového runbooku Event Gridu

1. Vyberte svůj účet Automation a vyberte stránku **Runbooky**.

   ![Výběr runbooků](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Vyberte tlačítko **Procházet galerii**.

3. Vyhledejte **Event Grid** a vyberte **Integrace Azure Automation se službou Event Grid**.

    ![Import runbooku galerie](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Vyberte **Importovat** a pojmenujte ho **Watch-VMWrite**.

5. Po importu vyberte **Upravit** a zobrazte zdroj runbooku. 
6. Aktualizujte řádek 74 ve skriptu, který chcete použít `Tag` místo `Tags` .

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. Vyberte tlačítko **Publikovat**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Vytvoření volitelného webhooku Microsoft Teams

1. Ve službě Microsoft Teams vyberte **Další možnosti** vedle názvu kanálu a pak vyberte **Konektory**.

    ![Připojení Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Procházejte seznamem konektorů, vyhledejte **Příchozí webhook** a vyberte **Přidat**.

3. Jako název zadejte **AzureAutomationIntegration** a vyberte **Vytvořit**.

4. Zkopírujte adresu URL Webhooku do schránky a uložte ji. Adresa URL webhooku se používá k odesílání informací do Microsoft Teams.

5. Vyberte **Hotovo** a uložte webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Vytvoření webhooku pro runbook

1. Otevřete runbook Watch-VMWrite.

2. Vyberte **Webhooky** a pak vyberte tlačítko **Přidat Webhook**.

3. Jako název zadejte **WatchVMEventGrid**. Zkopírujte adresu URL do schránky a uložte ji.

    ![Konfigurace názvu webhooku](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Vyberte **Nakonfigurovat parametry a nastavení spouštění** a zadejte adresu URL webhooku Microsoft Teams pro **CHANNELURL**. Ponechte **WEBHOOKDATA** prázdné.

    ![Konfigurace parametrů webhooku](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Vyberte **Vytvořit** a vytvořte webhook runbooku služby Automation.

## <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu

1. Na stránce s přehledem **Účet Automation** vyberte **Event Grid**.

    ![Výběr Event Gridu](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Klikněte na **+ Odběr události**.

3. Nakonfigurujte odběr podle následujících informací:
    1. Jako **Typ tématu** vyberte **Předplatná Azure**.
    2. Zrušte zaškrtnutí políčka **Přihlásit se k odběru všech typů událostí**.
    3. Jako název zadejte **AzureAutomation**.
    4. V rozevíracím seznamu **Definované typy událostí** zrušte zaškrtnutí všech možností kromě možnosti **Úspěšný zápis prostředku**.

        > [!NOTE] 
        > Azure Resource Manager aktuálně nerozlišuje mezi vytvářením a aktualizací, takže implementace tohoto kurzu pro všechny události Microsoft. Resources. ResourceWriteSuccess ve vašem předplatném Azure by mohla vést k velkému objemu volání.
    1. Jako **Typ koncového bodu** vyberte **Webhook**.
    2. Klikněte na **Vybrat koncový bod**. Na stránce **Vybrat webhook**, která se otevře, vložte adresu URL webhooku, který jste vytvořili pro runbook Watch-VMWrite.
    3. V části **FILTRY** zadejte předplatné a skupinu prostředků, kde chcete hledat nově vytvořené virtuální počítače. Mělo by to vypadat takto: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Výběrem možnosti **Vytvořit** uložte odběr Event Gridu.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Vytvoření virtuálního počítače, který spustí runbook

1. Vytvořte nový virtuální počítač ve skupině prostředků, kterou jste zadali ve filtru předpon odběru Event Gridu.

2. Měl by se zavolat runbook Watch-VMWrite a přidat se nová značka k virtuálnímu počítači.

    ![Značka virtuálního počítače](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Nová zpráva se odešle do kanálu Microsoft Teams.

    ![Oznámení Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte integraci služeb Event Grid a Automation. Naučili jste se:

> [!div class="checklist"]
> * Importovat ukázkový runbook Event Gridu
> * Vytvořit volitelný webhook Microsoft Teams
> * Vytvořit webhook pro runbook
> * Vytvoří odběr Event Gridu.
> * Vytvořit virtuální počítač, který spustí runbook

> [!div class="nextstepaction"]
> [Vytvoření a směrování vlastních událostí pomocí Event Gridu](../event-grid/custom-event-quickstart.md)
