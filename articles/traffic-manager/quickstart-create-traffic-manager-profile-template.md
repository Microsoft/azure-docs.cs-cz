---
title: 'Rychlý Start: vytvoření Traffic Manager pomocí šablony Azure Resource Manager (šablona ARM)'
description: Tento článek rychlý Start popisuje, jak vytvořit profil Azure Traffic Manager pomocí šablony Azure Resource Manager (šablona ARM).
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: dbdb6a255fdf0214103a0011f25b0a6d25014e69
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89299146"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Rychlý Start: vytvoření profilu Traffic Manager pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit profil Traffic Manager s externími koncovými body pomocí metody směrování výkonu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

V této šabloně je definovaný jeden prostředek Azure:

* [**Microsoft. Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Další šablony, které souvisejí s Azure Traffic Manager, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte **vyzkoušet** z následujícího bloku kódu a otevřete Azure Cloud Shell a pak postupujte podle pokynů pro přihlášení k Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Počkejte, dokud se nezobrazí výzva z konzoly.

1. Pro zkopírování skriptu PowerShellu vyberte **Kopírovat** z předchozího bloku kódu.

1. Klikněte pravým tlačítkem na Podokno konzole prostředí a pak vyberte **Vložit**.

1. Zadejte hodnoty.

    Nasazení šablony vytvoří profil se dvěma externími koncovými body. **Endpoint1** používá cílový koncový bod *w<span>TT. Microsoft</span>. com* s umístěním v **Severní Evropa**. **Pro endpoint2 u** používá cílový koncový bod *d<span>OCS. Microsoft</span>. com* s umístěním v **střed USA – jih**. 

    Název skupiny prostředků je název projektu s připojeným **RG** .

    > [!NOTE]
    > **uniqueDNSname** musí být globálně jedinečný název, aby bylo možné šablonu úspěšně nasadit. Pokud nasazení neproběhne úspěšně, začněte znovu s krokem 1.

    Nasazení šablony trvá několik minut. Výstup je po dokončení podobný tomuto:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Výstup nasazení PowerShellu pro Azure Traffic Manager Správce prostředků":::

Azure PowerShell slouží k nasazení šablony. Kromě Azure PowerShell můžete použít také Azure Portal, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

1. Pomocí [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)určete název DNS profilu Traffic Manager.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Zkopírujte hodnotu **RelativeDnsName** . Název DNS vašeho profilu Traffic Manager je *<* relativednsname *>. trafficmanager.NET*. 

1. Z místního prostředí PowerShell spusťte následující příkaz nahrazením proměnné **{relativeDNSname}** pomocí *<* relativeDNSname *>. trafficmanager.NET*.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```
    V závislosti na tom, která oblast je pro vás nejblíže, byste měli mít NameHost buď *w<span>TT. Microsoft</span>. com* nebo *d<span>OCS. Microsoft</span>. com* .

1. Pokud chcete zjistit, jestli se dá přeložit na jiný koncový bod, zakažte koncový bod pro cíl, který jste dostali v posledním kroku. Nahraďte **{Endpoint}** buď **endpoint1** nebo **pro endpoint2 u** , aby se zakázal cíl pro *w<span>TT. Microsoft</span>. com* nebo *d<span>OCS. Microsoft</span>. com* .

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```
1. Spusťte příkaz z kroku 2 znovu v místním prostředí PowerShell. Tentokrát byste měli získat další NameHost pro druhý koncový bod. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už profil Traffic Manager nepotřebujete, odstraňte skupinu prostředků. Tím se odebere profil Traffic Manager a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:
* Profil služby Traffic Manager

Pokud se chcete dozvědět víc o provozu směrování, přejděte k Traffic Manager kurzům.

> [!div class="nextstepaction"]
> [Kurzy pro službu Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
