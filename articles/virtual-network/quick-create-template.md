---
title: 'Rychlý Start: vytvoření virtuální sítě pomocí šablony Správce prostředků'
titleSuffix: Azure Virtual Network
description: Naučte se používat šablonu Správce prostředků k vytvoření virtuální sítě Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217664"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Rychlý Start: Vytvoření šablony Správce prostředků virtuální sítě

V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť se dvěma podsítěmi pomocí šablony Azure Resource Manager. Virtuální síť je základním stavebním blokem pro vaši privátní síť v Azure. Umožňuje prostředkům Azure, jako jsou virtuální počítače, bezpečně komunikovat mezi sebou a s internetem.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý Start můžete také dokončit pomocí [Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)nebo rozhraní příkazového [řádku Azure CLI](quick-create-cli.md).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json) .

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

V šabloně jsou definované následující prostředky Azure:
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): vytvořte virtuální síť Azure.
-  [**Microsoft. Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets) – vytvoření podsítě.

## <a name="deploy-the-template"></a>Nasazení šablony

Nasadit šablonu Správce prostředků do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří virtuální síť se dvěma podsítěmi.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. Na portálu na stránce **vytvořit Virtual Network se dvěma podsítěmi** zadejte nebo vyberte následující hodnoty:
   - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte název skupiny prostředků a vyberte **OK**.
   - **Virtual Network název**: zadejte název nové virtuální sítě.
3. Vyberte **Zkontrolovat a vytvořit** a potom vyberte **Vytvořit**.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Prozkoumejte prostředky, které byly vytvořeny pomocí virtuální sítě.

Další informace o syntaxi a vlastnostech JSON pro virtuální síť v šabloně najdete v tématu [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili ve virtuální síti, odstraňte skupinu prostředků. Tím se odebere virtuální síť a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste nasadili virtuální síť Azure se dvěma podsítěmi. Další informace o virtuálních sítích Azure najdete v kurzu pro virtuální sítě.

> [!div class="nextstepaction"]
> [Filtrování provozu sítě](tutorial-filter-network-traffic.md)