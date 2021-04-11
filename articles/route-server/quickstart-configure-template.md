---
title: 'Rychlý Start: vytvoření směrovacího serveru Azure pomocí šablony Azure Resource Manager (šablona ARM)'
description: V tomto rychlém startu se dozvíte, jak vytvořit směrovací server Azure pomocí šablony Azure Resource Manager (šablona ARM).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 6f56b9fb1f6a1f5a1fe0811617fb20412c52fd72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106452189"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Rychlý Start: vytvoření směrovacího serveru Azure pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager (šablonu ARM) k nasazení směrovacího serveru Azure do nové nebo existující virtuální sítě.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-route-server).

V tomto rychlém startu nasadíte směrovací server Azure do nové nebo existující virtuální sítě. `RouteServerSubnet`Pro hostování serveru směrování se vytvoří vyhrazená podsíť s názvem. Pro vytvoření partnerského vztahu protokolu BGP se dá server směrování taky nakonfigurovat s partnerským číslem ASN a partnerským IP adresou.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

V šabloně bylo definováno více prostředků Azure:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/virtualNetworks/podsítí**](/azure/templates/microsoft.network/virtualNetworks/subnets) (dvě podsítě, jedna s názvem `routeserversubnet` )
* [**Microsoft. Network/virtualHubs**](/azure.templates/microsoft.network/virtualhubs) (nasazení serveru tras)
* [**Microsoft. Network/virtualHubs/IPConfiguration**](/azure.templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft. Network/virtualHubs/bgpConnections**](/azure.templates/microsoft.network/virtualhubs/bgpConnections) (konfigurace partnerského ASN a partnerského protokolu IP)


Další šablony, které souvisejí s ExpressRoute, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte **vyzkoušet** z následujícího bloku kódu a otevřete Azure Cloud Shell a pak postupujte podle pokynů pro přihlášení k Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Počkejte, dokud se nezobrazí výzva z konzoly.

1. Pro zkopírování skriptu PowerShellu vyberte **Kopírovat** z předchozího bloku kódu.

1. Klikněte pravým tlačítkem na Podokno konzole prostředí a pak vyberte **Vložit**.

1. Zadejte hodnoty.

    Název skupiny prostředků je název projektu s připojeným **RG** .

    Nasazení šablony trvá přibližně 20 minut. Výstup je po dokončení podobný tomuto:

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Výstup nasazení Správce prostředků pro šablonu prostředí PowerShell serveru pro směrování":::

Azure PowerShell slouží k nasazení šablony. Kromě Azure PowerShell můžete použít také Azure Portal, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků** .

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je název projektu s připojeným **RG** .

1. Skupina prostředků by měla obsahovat jenom virtuální síť:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Skupina prostředků nasazení serveru směrování s virtuální sítí.":::

1. Přejděte na https://aka.ms/routeserver.

1. Výběrem směrovacího serveru s názvem **routeserver** ověřte, že nasazení proběhlo úspěšně.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Snímek stránky s přehledem serveru Směrování":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete prostředky, které jste vytvořili pomocí serveru směrování, odstraňte skupinu prostředků. Tím se odebere server směrování a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

* Server trasy
* Virtual Network
* Podsíť

Po vytvoření serveru Směrování Azure si přečtěte informace o tom, jak server tras Azure komunikuje s ExpressRoute a branami VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute a podpora Azure VPN](expressroute-vpn-support.md)
