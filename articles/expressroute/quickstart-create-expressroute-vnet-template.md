---
title: 'Rychlý Start: vytvoření okruhu ExpressRoute pomocí šablony Azure Resource Manager (šablona ARM)'
description: V tomto rychlém startu se dozvíte, jak vytvořit okruh ExpressRoute pomocí šablony Azure Resource Manager (šablona ARM).
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armsq
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 540d9d5f7ef3815743909a399076c55018f0df40
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961847"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Rychlý Start: vytvoření okruhu ExpressRoute s privátním partnerským vztahem pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit okruh ExpressRoute se soukromým partnerským vztahem.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

V tomto rychlém startu vytvoříte okruh ExpressRoute s *Equinix* jako poskytovatelem služeb. Okruh bude používat *SKU úrovně Premium*, se šířkou pásma *50 MB/* s a umístěním partnerského vztahu *řadiče domény Washington*. Privátní partnerské vztahy budou povolené s primární a sekundární podsítí *192.168.10.16/30* a *192.168.10.20/30* . Vytvoří se taky virtuální síť spolu s *bránou HighPerformance ExpressRoute*.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json" range="001-351" highlight="183-219":::

V šabloně bylo definováno více prostředků Azure:

* [**Microsoft. Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft. Network/expressRouteCircuits/Peers**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (používá se pro povolení privátního partnerského vztahu na okruhu)
* [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (skupina zabezpečení sítě se používá pro podsítě ve virtuální síti).
* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks) 
* [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (veřejná IP adresa je používána bránou ExpressRoute)
* [**Microsoft. Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (Brána ExpressRoute se používá k propojení virtuální sítě k okruhu).

Další šablony, které souvisejí s ExpressRoute, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte **vyzkoušet** z následujícího bloku kódu a otevřete Azure Cloud Shell a pak postupujte podle pokynů pro přihlášení k Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Výstup nasazení ExpressRoute Správce prostředků PowerShellu":::

Azure PowerShell slouží k nasazení šablony. Kromě Azure PowerShell můžete použít také Azure Portal, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků** .

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je název projektu s připojeným **RG** .

1. Skupina prostředků by měla obsahovat následující zdroje informací:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Výstup nasazení ExpressRoute Správce prostředků PowerShellu":::

1. Vyberte ExpressRoute okruh **ER-ck01** , abyste ověřili, že je **povolený**stav okruhu, stav poskytovatele není **zřízený** , a soukromý partnerský vztah má stav **zřízený**.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="Výstup nasazení ExpressRoute Správce prostředků PowerShellu":::

> [!NOTE]
> Před propojením virtuální sítě s okruhem bude nutné volat poskytovatele, aby bylo možné proces zřizování dokončit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v okruhu ExpressRoute, odstraňte skupinu prostředků. Tím se odstraní okruh ExpressRoute a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:
* Okruh ExpressRoute
* Virtual Network
* VPN Gateway
* Veřejná IP adresa
* skupiny zabezpečení sítě

Pokud se chcete dozvědět, jak propojit virtuální síť k okruhu, přejděte k ExpressRoute kurzům.

> [!div class="nextstepaction"]
> [Kurzy ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
