---
title: 'Rychlý Start: Vytvoření brány NAT – šablona Správce prostředků'
titleSuffix: Azure Virtual Network NAT
description: V tomto rychlém startu se dozvíte, jak vytvořit bránu NAT pomocí šablony Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: 5e0d62885598aea6f1656d0cdcef5f0c3406e7dd
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84341027"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Rychlý Start: Vytvoření brány NAT – šablona Správce prostředků

Začněte používat službu Virtual Network NAT pomocí Azure Resource Manager šablony.  Tato šablona nasadí virtuální síť, prostředek brány NAT a virtuální počítač s Ubuntu. Virtuální počítač Ubuntu je nasazený do podsítě, která je přidružená k prostředku brány NAT.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Vytvoření brány NAT a podpůrných prostředků

Tato šablona je nakonfigurovaná tak, aby vytvořila 

* Virtuální síť 
* Prostředek služby NAT Gateway
* Virtuální počítač Ubuntu

Virtuální počítač Ubuntu je nasazený do podsítě, která je přidružená k prostředku brány NAT.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json) .

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

V šabloně jsou definované devět prostředků Azure:

**Microsoft.Network**

* **[Microsoft. Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: vytvoří prostředek brány NAT.

* **[Microsoft. Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: vytvoří skupinu zabezpečení sítě.

    * **[Microsoft. Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: vytvoří pravidlo zabezpečení.

* **[Microsoft. Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)**: Vytvoří veřejnou IP adresu.

* **[Microsoft. Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: vytvoří předponu veřejné IP adresy.

* **[Microsoft. Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: vytvoří virtuální síť.

    * **[Microsoft. Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: vytvoří podsíť virtuální sítě.

* **[Microsoft. Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: vytvoří síťové rozhraní.

**Microsoft.Compute**

* **[Microsoft. COMPUTE/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: vytvoří virtuální počítač.

### <a name="deploy-the-template"></a>Nasazení šablony

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

**Portál Azure Portal**

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

2. V levém podokně vyberte **skupiny prostředků** .

3. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je **myResourceGroupNAT**

4. Ověřte, že se ve skupině prostředků vytvořily tyto prostředky:

    ![Virtual Network skupina prostředků NAT](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

**Azure CLI**

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Portál Azure Portal**

Pokud už je nepotřebujete, odstraňte skupinu prostředků, bránu NAT a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupNAT** , která obsahuje bránu NAT, a pak vyberte **Odstranit**.

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
