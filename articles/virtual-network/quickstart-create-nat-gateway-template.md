---
title: Vytvoření brány NAT – šablona Správce prostředků
titleSuffix: Azure Virtual Network NAT
description: V tomto rychlém startu se dozvíte, jak vytvořit bránu NAT pomocí šablony Azure Resource Manager (šablona ARM).
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 95856db9288e5860dfab47dce506d1e7d6de1ffc
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913326"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Rychlý Start: Vytvoření šablony NAT Gateway – ARM

Začínáme se službou Virtual Network NAT pomocí šablony Azure Resource Manager (šablona ARM). Tato šablona nasadí virtuální síť, prostředek brány NAT a virtuální počítač s Ubuntu. Virtuální počítač Ubuntu je nasazený do podsítě, která je přidružená k prostředku brány NAT.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure** . Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Tato šablona je nakonfigurovaná tak, aby vytvořila:

* Virtuální síť
* Prostředek služby NAT Gateway
* Virtuální počítač Ubuntu

Virtuální počítač Ubuntu se nasadí do podsítě, která je přidružená k prostředku brány NAT.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

V šabloně jsou definované devět prostředků Azure:

* **[Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)** : vytvoří skupinu zabezpečení sítě.
* **[Microsoft. Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : vytvoří pravidlo zabezpečení.
* **[Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)** : Vytvoří veřejnou IP adresu.
* **[Microsoft. Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)** : vytvoří předponu veřejné IP adresy.
* **[Microsoft. COMPUTE/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)** : vytvoří virtuální počítač.
* **[Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)** : vytvoří virtuální síť.
* **[Microsoft. Network/natGateways](/azure/templates/microsoft.network/natgateways)** : vytvoří prostředek brány NAT.
* **[Microsoft. Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)** : vytvoří podsíť virtuální sítě.
* **[Microsoft. Network/networkinterfaces](/azure/templates/microsoft.network/networkinterfaces)** : vytvoří síťové rozhraní.

## <a name="deploy-the-template"></a>Nasazení šablony

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků** .

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je **myResourceGroupNAT**

1. Ověřte, že se ve skupině prostředků vytvořily tyto prostředky:

    ![Virtual Network skupina prostředků NAT](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

**Azure CLI**

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Pokud už je nepotřebujete, odstraňte skupinu prostředků, bránu NAT a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupNAT** , která obsahuje bránu NAT, a pak vyberte **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

* Prostředek služby NAT Gateway
* Virtuální síť
* Virtuální počítač Ubuntu

Virtuální počítač je nasazený do podsítě virtuální sítě přidružené k bráně NAT.

Další informace o Virtual Network NAT a Azure Resource Manager najdete dál v článcích níže.

* Přečtěte si [přehled Virtual Network NAT](nat-overview.md)
* Přečtěte si o [prostředku brány NAT](nat-gateway-resource.md)
* Další informace o [Azure Resource Manageru](../azure-resource-manager/management/overview.md)
