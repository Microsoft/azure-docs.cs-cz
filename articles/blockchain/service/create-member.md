---
title: Create an Azure Blockchain Service member - Azure portal
description: Create an Azure Blockchain Service member for a blockchain consortium using the Azure portal.
ms.date: 11/18/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 90f859e4d2e2621afad9cab11d66e81018bd3147
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455781"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Quickstart: Create an Azure Blockchain Service blockchain member using the Azure portal

In this quickstart, you deploy a new blockchain member and consortium in Azure Blockchain Service using the Azure portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Vytvoření člena blockchainu

Create a blockchain member that runs the Quorum ledger protocol in a new or existing consortium.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
1. Select **Blockchain** > **Azure Blockchain Service (preview)** .

    ![Create Service](./media/create-member/create-member.png)

    Nastavení | Popis
    --------|------------
    Předplatné | Select the Azure subscription that you want to use for your service. Pokud máte více předplatných, zvolte předplatné, ve kterém se vám prostředek účtuje.
    Skupina prostředků | Create a new resource group name or choose an existing one from your subscription.
    Oblast | Choose a region to create the member. All members of the consortium must be in the same location.
    Protocol (Protokol) | Currently, Azure Blockchain Service Preview supports the Quorum protocol.
    Konsorcium | For a new consortium, enter a unique name. If joining a consortium through an invite, choose the consortium you are joining.
    Name (Název) | Choose a unique name for the Azure Blockchain Service member. The blockchain member name can only contain lowercase letters and numbers. Prvním znakem musí být písmeno. The value must be between 2 and 20 characters long.
    Member account password | The member account password is used to encrypt the private key for the Ethereum account that is created for your member. You use the member account and member account password for consortium management.
    Popis | Description of the consortium.
    Ceny | The node configuration and cost for your new service. Select the **Change** link to choose between **Standard** and **Basic** tiers.
    Node password | The password for the member's default transaction node. Use the password for basic authentication when connecting to blockchain member's default transaction node public endpoint.

1. Select **Review + create** to validate your settings. Select **Create** to provision the service. Provisioning takes about 10 minutes.
1. Select **Notifications** on the toolbar to monitor the deployment process.
1. After deployment, navigate to your blockchain member.

Select **Overview**, you can view the basic information about your service including the RootContract address and member account.

![Blockchain member overview](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

You can use the member you created for the next quickstart or tutorial. When no longer needed, you can delete the resources by deleting the `myResourceGroup` resource group you created for the quickstart.

To delete the resource group:

1. In the Azure portal, navigate to **Resource group** in the left navigation pane and select the resource group you want to delete.
2. Vyberte **Odstranit skupinu prostředků**. Verify deletion by entering the resource group name and select **Delete**.

## <a name="next-steps"></a>Další kroky

In this quickstart, you deployed an Azure Blockchain Service member and a new consortium. Try the next quickstart to use Azure Blockchain Development Kit for Ethereum to attach to a consortium on Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Use Visual Studio Code to connect to Azure Blockchain Service](connect-vscode.md)
