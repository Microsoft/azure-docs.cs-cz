---
title: Rychlý Start – vytvoření privátního koncového bodu Azure pomocí Azure CLI
description: V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit privátní koncový bod.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: f74a143859f0a6629c88f0dcb61a97697f49d0be
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889225"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure CLI

Začínáme s privátním koncovým bodem Azure pomocí privátního koncového bodu pro zabezpečené připojení k webové aplikaci Azure.

V tomto rychlém startu vytvoříte privátní koncový bod pro webovou aplikaci Azure a nasadíte virtuální počítač pro otestování privátního připojení.  

Pro různé druhy služeb Azure, jako je Azure SQL a Azure Storage, je možné vytvořit privátní koncové body.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Webovou aplikaci Azure s **PremiumV2** nebo vyšším plánem služby App Service nasazeným ve vašem předplatném Azure.  
    * Další informace a příklad najdete v tématu [rychlý Start: Vytvoření webové aplikace v ASP.NET Core v Azure](../app-service/quickstart-dotnetcore.md). 
    * Podrobný kurz týkající se vytvoření webové aplikace a koncového bodu najdete v tématu [kurz: připojení k webové aplikaci pomocí privátního koncového bodu Azure](tutorial-private-endpoint-webapp-portal.md).
* Přihlaste se k Azure Portal a ověřte, že je vaše předplatné aktivní spuštěním `az login` .
* Podívejte se na verzi rozhraní příkazového řádku Azure CLI v terminálu nebo příkazovém okně spuštěním příkazu `az --version` . Nejnovější verzi najdete v [poznámkách k nejnovější verzi](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Pokud nemáte nejnovější verzi, aktualizujte instalaci pomocí [instalační příručky pro váš operační systém nebo platformu](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az_group_create):

* Název **CreatePrivateEndpointQS-RG**. 
* V umístění **eastus** .

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Vytvoření virtuální sítě a hostitele bastionu

V této části vytvoříte virtuální síť, podsíť a hostitele bastionu. 

Hostitel bastionu se bude používat k zabezpečenému připojení k virtuálnímu počítači za účelem testování privátního koncového bodu.

Vytvoření virtuální sítě pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create)

* S názvem **myVNet**.
* Předpona adresy **10.0.0.0/16**.
* Podsíť s názvem **myBackendSubnet**.
* Předpona podsítě **10.0.0.0/24**.
* Ve skupině prostředků **CreatePrivateEndpointQS-RG** .
* Umístění **eastus**

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Aktualizace podsítě pro zakázání zásad privátního koncového bodu pro privátní koncový bod pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

K vytvoření veřejné IP adresy pro hostitele bastionu použijte [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) :

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myBastionIP**.
* V **CreatePrivateEndpointQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) vytvořte podsíť bastionu:

* S názvem **AzureBastionSubnet**.
* Předpona adresy **10.0.1.0/24**.
* Ve virtuální síti **myVNet**.
* Ve skupině prostředků **CreatePrivateEndpointQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Pomocí [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) Vytvořte hostitele bastionu:

* S názvem **myBastionHost**.
* V **CreatePrivateEndpointQS-RG**.
* Přidruženo k veřejné IP **myBastionIP**.
* Přidruženo k virtuální síti **myVNet**.
* V umístění **eastus** .

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Nasazení hostitele Azure bastionu může trvat několik minut.

## <a name="create-test-virtual-machine"></a>Vytvořit testovací virtuální počítač

V této části vytvoříte virtuální počítač, který se použije k otestování privátního koncového bodu.

Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az_vm_create). Po zobrazení výzvy zadejte heslo, které se má použít jako přihlašovací údaje pro virtuální počítač:

* S názvem **myVM**.
* V **CreatePrivateEndpointQS-RG**.
* V Network **myVNet**.
* V **myBackendSubnet** podsíti.
* **Win2019Datacenter** image serveru.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

V této části vytvoříte privátní koncový bod.

Pomocí [AZ WebApp list](/cli/azure/webapp#az_webapp_list) umístěte ID prostředku webové aplikace, kterou jste předtím vytvořili do proměnné prostředí.

Pomocí [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) vytvořte koncový bod a připojení:

* S názvem **myPrivateEndpoint**.
* Ve skupině prostředků **CreatePrivateEndpointQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Připojení s názvem **mojepripojeni**.
* Vaše WebApp **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS

V této části vytvoříte a nakonfigurujete privátní zónu DNS pomocí [AZ Network Private-DNS Zone Create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Pomocí [AZ Network Private-DNS Link VNet Create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) vytvoříte propojení virtuální sítě k zóně DNS.

Vytvoříte skupinu zón DNS pomocí [AZ Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zóna s názvem **privatelink.azurewebsites.NET**
* Ve virtuální síti **myVNet**.
* Ve skupině prostředků **CreatePrivateEndpointQS-RG**.
* Odkaz DNS s názvem **myDNSLink**.
* Přidruženo k **myPrivateEndpoint**.
* Skupina zón s názvem **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
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
Po dokončení používání privátního koncového bodu a virtuálního počítače odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [AZ Group Delete](/cli/azure/group#az_group_delete) :

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
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
