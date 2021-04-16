---
title: 'Kurz: Vytvoření brány NAT – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Začněte vytvářet bránu NAT pomocí Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: d312702f441cfe2ad94e347cadcdfc88d4cc2a72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479317"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Kurz: Vytvoření brány NAT pomocí Azure CLI

V tomto kurzu se dozvíte, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT pro poskytování odchozího připojení pro virtuální počítač v Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte virtuální síť.
> * Vytvoří virtuální počítač.
> * Vytvořte bránu NAT a přidružte ji k virtuální síti.
> * Připojte se k virtuálnímu počítači a ověřte IP adresu NAT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

V této části vytvoříme bránu NAT a podpůrné prostředky.

### <a name="create-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro přístup k Internetu potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create) vytvořte prostředek veřejné IP adresy s názvem **myPublicIP** v **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>Vytvořit prostředek brány NAT

Vytvořte globální bránu Azure NAT pomocí [AZ Network NAT Gateway Create](/cli/azure/network/nat#az_network_nat_gateway_create). Výsledkem tohoto příkazu je vytvoření prostředku brány s názvem **myNATgateway** , který používá veřejnou IP adresu **myPublicIP**. Časový limit nečinnosti je nastavený na 10 minut.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create) ve skupině prostředků **myResourceGroup** . Adresní prostor IP adres pro virtuální síť je **10.1.0.0/16**. Podsíť ve virtuální síti je **10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Vytvořit hostitele bastionu

Pro přístup k virtuálnímu počítači vytvořte hostitele Azure bastionu s názvem **myBastionHost** . 

Pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) vytvořte podsíť Azure bastionu.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Vytvořte veřejnou IP adresu pro hostitele bastionu pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

K vytvoření hostitele bastionu použijte [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) . 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurace služby NAT pro zdrojovou podsíť

Nakonfigurujeme **mySubnet** zdrojové podsítě ve službě Virtual Network **myVnet** tak, aby používala konkrétní prostředek brány NAT **myNATgateway** pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Tento příkaz aktivuje službu NAT v zadané podsíti.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Veškerý odchozí provoz do internetových cílů teď používá bránu NAT.  Není nutné konfigurovat UDR.


## <a name="virtual-machine"></a>Virtuální počítač

V této části vytvoříte virtuální počítač pro otestování brány NAT, abyste ověřili veřejnou IP adresu odchozího připojení.

Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Než přejdete k další části, počkejte, než se dokončí vytváření virtuálního počítače.

## <a name="test-nat-gateway"></a>Test brány NAT

V této části otestujeme bránu NAT. Nejdříve zjistíme veřejnou IP adresu brány NAT. Pak se připojíme k testovacímu virtuálnímu počítači a ověříme odchozí připojení prostřednictvím brány NAT.
    
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu pro bránu NAT. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myPublicIP**.

2. Poznamenejte si veřejnou IP adresu:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Zjistit veřejnou IP adresu brány NAT" border="true":::

3. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom v seznamu prostředky vyberte **myVM** , která je umístěná ve skupině prostředků **myResourceGroupNAT** .

4. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

5. Vyberte tlačítko modrého **použití bastionu** .

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Otevřete **Internet Explorer** v **myTestVM**.

8. **https://whatsmyip.com** Do adresního řádku zadejte.

9. Ověřte, že zobrazená IP adresa odpovídá adrese brány NAT, kterou jste si poznamenali v předchozím kroku:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer zobrazující externí odchozí IP adresy" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte virtuální síť, virtuální počítač a bránu NAT pomocí následujících kroků:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Virtual Network NAT najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Přehled služby Virtual Network NAT](nat-overview.md)
