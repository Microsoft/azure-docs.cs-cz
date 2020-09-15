---
title: Vytvoření interního nástroje pro vyrovnávání zatížení pomocí šablony Azure Resource Manager (šablona ARM)
description: Naučte se vytvořit interní nástroj pro vyrovnávání zatížení Azure pomocí šablony Azure Resource Manager (šablona ARM).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 287afc51aa15ed4cadba7e2d6cd389e4869d7d61
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532853"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit interní nástroj pro vyrovnávání zatížení Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer).

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

V šabloně bylo definováno více prostředků Azure:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): účty úložiště virtuálních počítačů pro diagnostiku spouštění.
- [**Microsoft. COMPUTE/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): Skupina dostupnosti pro virtuální počítače.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): Virtual Network pro nástroj pro vyrovnávání zatížení a virtuální počítače.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): síťová rozhraní pro virtuální počítače.
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): interní nástroj pro vyrovnávání zatížení.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): virtuální počítače.

Další šablony, které souvisejí s Azure Load Balancer, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém podokně vyberte **skupiny prostředků** .

3. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je **myResourceGroupLB**

4. Ověřte, že se ve skupině prostředků vytvořily tyto prostředky:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Uživatel Azure Portal pro ověření vytváření prostředků." border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Další kroky

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony ARM](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)