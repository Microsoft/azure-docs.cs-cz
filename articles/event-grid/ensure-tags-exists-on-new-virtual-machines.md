---
title: Integrovat mřížky událostí služby Azure Automation | Microsoft Docs
description: Zjistěte, jak se automaticky při vytvoření nového virtuálního počítače přidat značku a odeslat oznámení Teams společnosti Microsoft.
keywords: automatizace sady runbook, týmů, události mřížky, virtuálního počítače,
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
ms.openlocfilehash: 9a4d6ecf19fc96a9c7b92cf246effbf3948fb478
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2017
ms.locfileid: "26349065"
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Integrovat událostí mřížky a týmy pro Microsoft Azure Automation.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importujte ukázkové sady runbook událostí mřížky.
> * Vytvořte webhook volitelné Teams společnosti Microsoft.
> * Vytvoření webhooku pro sadu runbook.
> * Vytvořte předplatné událostí mřížky.
> * Vytvořte virtuální počítač, který aktivuje sady runbook.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu [účet Azure Automation](../automation/automation-offering-get-started.md) je požadované pro uchovávání sady runbook, která se aktivuje z předplatného Azure událostí mřížky.

## <a name="import-an-event-grid-sample-runbook"></a>Importovat ukázkové sady runbook událostí mřížky
1. Vyberte svůj účet Automation a vyberte **Runbooky** stránky.

   ![Vyberte sady runbook](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Vyberte **procházet galerii** tlačítko.

3. Vyhledejte **událostí mřížky**a vyberte **integrace Azure Automation s událostí mřížky**. 

    ![Importovat Galerie sady runbook](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Vyberte **Import** a pojmenujte ji **sledovat VMWrite**.

5. Potom, co se importuje, vyberte **upravit** zobrazení zdroje sady runbook. Vyberte tlačítko **Publikovat**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Vytvoření webhook volitelné Teams společnosti Microsoft
1. V Teams společnosti Microsoft, vyberte **další možnosti** další název kanálu a pak vyberte **konektory**.

    ![Připojení Teams společnosti Microsoft](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Procházení seznam konektorů k **příchozí Webhooku**a vyberte **přidat**.

3. Zadejte **AzureAutomationIntegration** pro název a vyberte **vytvořit**.

4. Webhook zkopírujte do schránky a uložte jej. Adresa URL webhooku se používá k odeslání informací do Teams společnosti Microsoft.

5. Vyberte **provádí** uložit webhooku.

## <a name="create-a-webhook-for-the-runbook"></a>Vytvoření webhooku pro sadu runbook
1. Otevřete sadu runbook VMWrite sledovat.

2. Vyberte **Webhooky**a vyberte **přidat Webhooku** tlačítko.

3. Zadejte **WatchVMEventGrid** pro název. Zkopírujte adresu URL do schránky a uložte ho.

    ![Nakonfigurujte název webhooku](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Vyberte **nakonfigurovat parametry a nastavení spouštění**a zadejte adresu URL webhooku Teams společnosti Microsoft pro **CHANNELURL**. Nechte **WEBHOOKDATA** prázdné.

    ![Konfigurovat parametry webhooku](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Vyberte **OK** vytvořit webhook automatizace sady runbook.


## <a name="create-an-event-grid-subscription"></a>Vytvořte předplatné událostí mřížky
1. Na **účet Automation** Přehled stránky, vyberte **událostí mřížky**.

    ![Vyberte události mřížky](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Vyberte **+ předplatného události** tlačítko.

3. Konfigurace odběru s následujícími informacemi:

    *   Zadejte **AzureAutomation** pro název.
    *   V **typu tématu**, vyberte **předplatných Azure**.
    *   Vymazat **přihlášení k odběru pro všechny typy událostí** zaškrtávací políčko.
    *   V **typů událostí**, vyberte **prostředků zápisu úspěch**.
    *   V **koncový bod odběratele**, zadejte adresu URL webhooku pro sadu runbook VMWrite sledovat.
    *   V **předpony filtru**, zadejte předplatné a k vytvoření skupiny prostředků, ve které chcete vyhledat nové virtuální počítače. By měl vypadat jako:`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Vyberte **vytvořit** se uložení odběru událostí mřížky.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Vytvořit virtuální počítač, který aktivuje sady runbook
1. Vytvořte nový virtuální počítač ve skupině prostředků, které jste zadali v případě, že předplatné mřížky předpony filtru.

2. Kukátko – VMWrite runbook by měla být volána, novou značku přidat do virtuálního počítače.

    ![Značka virtuálních počítačů](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Nové zprávy se odesílá do kanálu Teams společnosti Microsoft.

    ![Oznámení Teams společnosti Microsoft](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu nastavíte integraci mezi událostí mřížky a automatizace. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Importujte ukázkové sady runbook událostí mřížky.
> * Vytvořte webhook volitelné Teams společnosti Microsoft.
> * Vytvoření webhooku pro sadu runbook.
> * Vytvořte předplatné událostí mřížky.
> * Vytvořte virtuální počítač, který aktivuje sady runbook.

> [!div class="nextstepaction"]
> [Vytvoření a trasy vlastních událostí s událostí mřížky](../event-grid/custom-event-quickstart.md)
