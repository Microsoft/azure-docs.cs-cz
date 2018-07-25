---
title: Integrace služby Azure Automation se službou Event Grid | Microsoft Docs
description: Zjistěte, jak automaticky přidat značku, když se vytvoří nový virtuální počítač, a odeslat oznámení do Microsoft Teams.
keywords: automation, runbook, teams, event grid, virtual machine, VM
services: automation
documentationcenter: ''
author: eamonoreilly
manager: ''
editor: ''
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: eamono
ms.openlocfilehash: 6270f8bad893798f46d8db91e7b1140b6a125350
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049860"
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Integrace služby Azure Automation se službou Event Grid a Microsoft Teams

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importovat ukázkový runbook Event Gridu
> * Vytvořit volitelný webhook Microsoft Teams
> * Vytvořit webhook pro runbook
> * Vytvoří odběr Event Gridu.
> * Vytvořit virtuální počítač, který spustí runbook

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu je potřeba [účet Azure Automation](../automation/automation-offering-get-started.md) pro uložení runbooku, který se spouští z předplatného Azure Event Gridu.

* Modul `AzureRM.Tags` musí být načtený v účtu Automation. Způsob importu modulů do Azure Automation najdete v [popisu importu modulů do Azure Automation](../automation/automation-update-azure-modules.md).

## <a name="import-an-event-grid-sample-runbook"></a>Import ukázkového runbooku Event Gridu

1. Vyberte svůj účet Automation a vyberte stránku **Runbooky**.

   ![Výběr runbooků](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Vyberte tlačítko **Procházet galerii**.

3. Vyhledejte **Event Grid** a vyberte **Integrace Azure Automation se službou Event Grid**.

    ![Import runbooku galerie](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Vyberte **Importovat** a pojmenujte ho **Watch-VMWrite**.

5. Po importu vyberte **Upravit** a zobrazte zdroj runbooku. Vyberte tlačítko **Publikovat**.

> [!NOTE]
> Řádek 74 ve skriptu je potřeba upravit na `Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose`. Parametr `-Tags` je teď `-Tag`.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Vytvoření volitelného webhooku Microsoft Teams

1. Ve službě Microsoft Teams vyberte **Další možnosti** vedle názvu kanálu a pak vyberte **Konektory**.

    ![Připojení Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Procházejte seznamem konektorů, vyhledejte **Příchozí webhook** a vyberte **Přidat**.

3. Jako název zadejte **AzureAutomationIntegration** a vyberte **Vytvořit**.

4. Zkopírujte webhook do schránky a uložte ho. Adresa URL webhooku se používá k odesílání informací do Microsoft Teams.

5. Vyberte **Hotovo** a uložte webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Vytvoření webhooku pro runbook

1. Otevřete runbook Watch-VMWrite.

2. Vyberte **Webhooky** a pak vyberte tlačítko **Přidat Webhook**.

3. Jako název zadejte **WatchVMEventGrid**. Zkopírujte adresu URL do schránky a uložte ji.

    ![Konfigurace názvu webhooku](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Vyberte **Nakonfigurovat parametry a nastavení spouštění** a zadejte adresu URL webhooku Microsoft Teams pro **CHANNELURL**. Ponechte **WEBHOOKDATA** prázdné.

    ![Konfigurace parametrů webhooku](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Vyberte **OK** a vytvořte webhook runbooku služby Automation.


## <a name="create-an-event-grid-subscription"></a>Vytvoření odběru Event Gridu
1. Na stránce s přehledem **Účet Automation** vyberte **Event Grid**.

    ![Výběr Event Gridu](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Vyberte tlačítko **+ Odběr události**.

3. Nakonfigurujte odběr podle následujících informací:

    *   Jako název zadejte **AzureAutomation**.
    *   V části **Typ tématu** vyberte **Předplatná Azure**.
    *   Zrušte zaškrtnutí políčka **Přihlásit se k odběru všech typů událostí**.
    *   V části **Typy událostí** vyberte **Úspěšný zápis prostředku**.
    *   Do pole **Koncový bod odběratele** zadejte adresu URL webhooku pro runbook Watch-VMWrite.
    *   V části **Filtr předpon** zadejte předplatné a skupinu prostředků, kde chcete hledat nově vytvořené virtuální počítače. Mělo by to vypadat takto: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Výběrem možnosti **Vytvořit** uložte odběr Event Gridu.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Vytvoření virtuálního počítače, který spustí runbook
1. Vytvořte nový virtuální počítač ve skupině prostředků, kterou jste zadali ve filtru předpon odběru Event Gridu.

2. Měl by se zavolat runbook Watch-VMWrite a přidat se nová značka k virtuálnímu počítači.

    ![Značka virtuálního počítače](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Nová zpráva se odešle do kanálu Microsoft Teams.

    ![Oznámení Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu nastavíte integraci služeb Event Grid a Automation. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Importovat ukázkový runbook Event Gridu
> * Vytvořit volitelný webhook Microsoft Teams
> * Vytvořit webhook pro runbook
> * Vytvoří odběr Event Gridu.
> * Vytvořit virtuální počítač, který spustí runbook

> [!div class="nextstepaction"]
> [Vytvoření a směrování vlastních událostí pomocí Event Gridu](../event-grid/custom-event-quickstart.md)
