---
title: Rychlý Start – vytvoření privátního koncového bodu Azure pomocí Azure PowerShell
description: V tomto rychlém startu se dozvíte, jak pomocí Azure PowerShell vytvořit privátní koncový bod.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: afd1f8b6c80cfd7aa8d5142841458d76511a9e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562907"
---
# <a name="quickstart-create-an-azure-private-endpoint-using-azure-powershell"></a>Rychlý Start: Vytvoření privátního koncového bodu Azure pomocí Azure PowerShell

Začínáme s privátním koncovým bodem Azure pomocí privátního koncového bodu pro zabezpečené připojení k webové aplikaci Azure.

V tomto rychlém startu vytvoříte privátní koncový bod pro webovou aplikaci Azure a nasadíte virtuální počítač pro otestování privátního připojení.  

Pro různé druhy služeb Azure, jako je Azure SQL a Azure Storage, je možné vytvořit privátní koncové body.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Webovou aplikaci Azure s **PremiumV2** nebo vyšším plánem služby App Service nasazeným ve vašem předplatném Azure.  
    * Další informace a příklad najdete v tématu [rychlý Start: Vytvoření webové aplikace v ASP.NET Core v Azure](../app-service/quickstart-dotnetcore.md). 
    * Podrobný kurz týkající se vytvoření webové aplikace a koncového bodu najdete v tématu [kurz: připojení k webové aplikaci pomocí privátního koncového bodu Azure](tutorial-private-endpoint-webapp-portal.md).

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

Vytvořte virtuální síť a hostitele bastionu pomocí:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

Nasazení hostitele Azure bastionu může trvat několik minut.

## <a name="create-test-virtual-machine"></a>Vytvořit testovací virtuální počítač

V této části vytvoříte virtuální počítač, který se použije k otestování privátního koncového bodu.

Vytvořit virtuální počítač pomocí:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

V této části vytvoříte privátní koncový bod a připojení pomocí:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS

V této části vytvoříte a nakonfigurujete privátní zónu DNS pomocí:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>Test připojení k privátnímu koncovému bodu

V této části použijete virtuální počítač, který jste vytvořili v předchozím kroku, abyste se připojili k SQL serveru přes soukromý koncový bod.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). 
 
2. V levém navigačním podokně vyberte **skupiny prostředků** .

3. Vyberte **CreatePrivateEndpointQS-RG**.

4. Vyberte **myVM**.

5. Na stránce Přehled pro **myVM** vyberte **připojit** a pak **bastionu**.

6. Vyberte tlačítko modrého **použití bastionu** .

7. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

8. Po připojení otevřete Windows PowerShell na serveru.

9. Zadejte `nslookup <your-webapp-name>.azurewebsites.net`. Nahraďte **\<your-webapp-name>** názvem webové aplikace, kterou jste vytvořili v předchozích krocích.  Zobrazí se zpráva podobná tomu, co se zobrazuje níže:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Pro název webové aplikace se vrátí privátní IP adresa **10.0.0.5** .  Tato adresa je v podsíti virtuální sítě, kterou jste vytvořili dříve.

10. V připojení bastionu k **myVM** otevřete Internet Explorer.

11. Zadejte adresu URL vaší webové aplikace, **https:// \<your-webapp-name> . azurewebsites.NET**.

12. Pokud vaše aplikace nebyla nasazena, obdržíte výchozí stránku webové aplikace:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Výchozí stránka webové aplikace" border="true":::

13. Ukončete připojení k **myVM**.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Po dokončení používání privátního koncového bodu a virtuálního počítače odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili:

* Virtuální síť a hostitel bastionu
* Virtuální počítač.
* Soukromý koncový bod pro webovou aplikaci Azure.

Virtuální počítač jste použili k zabezpečenému otestování připojení k webové aplikaci v rámci privátního koncového bodu.

Další informace o službách, které podporují soukromý koncový bod, najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Dostupnost privátního propojení](private-link-overview.md#availability)
