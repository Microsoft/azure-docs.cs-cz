---
title: 'Úvodní příručka: Vytvoření brány NAT – šablona Správce prostředků'
titleSuffix: Azure Virtual Network NAT
description: Tento rychlý start ukazuje, jak vytvořit bránu NAT pomocí šablony Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: 3850f3f22e730e46f6d278b6cef0e17d357b126d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618046"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Úvodní příručka: Vytvoření brány NAT – šablona Správce prostředků

Můžete začít s virtuální síť NAT pomocí šablony Správce prostředků Azure.  Tato šablona nasazuje virtuální síť, prostředek brány NAT a virtuální počítač Ubuntu. Virtuální počítač Ubuntu se nasadí do podsítě, která je přidružena k prostředku brány NAT.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Vytvoření brány NAT a podpůrných prostředků

Tato šablona je nakonfigurována tak, aby 

* Virtuální síť 
* Prostředek brány NAT
* Ubuntu virtuální stroj

Virtuální počítač Ubuntu se nasadí do podsítě, která je přidružená k prostředku brány NAT.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

V šabloně je definováno devět prostředků Azure:

**Microsoft.Network**

* **[Microsoft.Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: Vytvoří prostředek brány NAT.

* **[Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: Vytvoří skupinu zabezpečení sítě.

    * **[Microsoft.Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: Vytvoří pravidlo zabezpečení.

* **[Microsoft.Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)**: Vytvoří veřejnou IP adresu.

* **[Microsoft.Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: Vytvoří veřejnou předponu IP.

* **[Microsoft.Network/virtualNetworks:](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)** Vytvoří virtuální síť.

    * **[Microsoft.Network/virtualNetworks/podsítě](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: Vytvoří podsíť virtuální sítě.

* **[Microsoft.Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: Vytvoří síťové rozhraní.

**Microsoft.Compute**

* **[Microsoft.Compute/virtualMachines:](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)** Vytvoří virtuální počítač.

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

**portál Azure**

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém podokně vyberte **skupiny prostředků.**

3. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je **myResourceGroupNAT.**

4. Ověřte, zda byly ve skupině prostředků vytvořeny následující prostředky:

    ![Skupina prostředků překladu síťových sítí](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

**Azure CLI**

Pokud již není potřeba, můžete použít příkaz [odstranění skupiny az](/cli/azure/group#az-group-delete) k odebrání skupiny prostředků a všech prostředků obsažených v této oblasti.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Pokud již není potřeba, můžete použít [příkaz Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) k odebrání skupiny prostředků a všech prostředků obsažených v rámci.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**portál Azure**

Pokud již není potřeba, odstraňte skupinu prostředků, bránu NAT a všechny související prostředky. Vyberte skupinu prostředků **myResourceGroupNAT,** která obsahuje bránu NAT, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

* Prostředek brány NAT
* Virtuální síť
* Ubuntu virtuální stroj

Virtuální počítač se nasadí do podsítě virtuální sítě přidružené k bráně NAT. 

Další informace o virtuální síti NAT a Azure Resource Manager, pokračujte v článcích níže.

* Přečtěte si [přehled virtuální sítě NAT](nat-overview.md)
* Přečtěte si o [prostředku brány NAT](nat-gateway-resource.md)
* Další informace o [Azure Resource Manageru](../azure-resource-manager/management/overview.md)
