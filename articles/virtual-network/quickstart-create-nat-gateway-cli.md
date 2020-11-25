---
title: Vytvoření brány NAT – Azure CLI
titlesuffix: Azure Virtual Network NAT
description: V tomto rychlém startu se dozvíte, jak vytvořit bránu NAT pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: a4d034aefe59a661bfb0694feba36a669aa274ac
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007269"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Vytvoření brány NAT pomocí Azure CLI

V tomto kurzu se dozvíte, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT pro poskytování odchozího připojení pro virtuální počítač v Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.71 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro přístup k veřejnému Internetu potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIP** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření předpony veřejné IP adresy

Pomocí brány NAT můžete použít jeden nebo více prostředků veřejné IP adresy, předpony veřejných IP adres nebo obojí. V tomto scénáři přidáme prostředek s předponou veřejné IP adresy, abychom mohli Ukázat.   Pomocí [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) vytvořte prostředek předpony veřejné IP adresy s názvem **myPublicIPprefix** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

Tato část podrobně popisuje, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a předponu veřejných IP adres, které se mají použít pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minuty na 10 minut.

Vytvořte globální bránu Azure NAT pomocí [AZ Network NAT Gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) s názvem **myNATgateway**. Příkaz používá veřejnou IP adresu **myPublicIP** i předponu veřejné IP adresy **myPublicIPprefix**. Příkaz změní časový limit nečinnosti na **10** minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

V tomto okamžiku je brána NAT funkční a všechny, které chybí, je konfigurace, které podsítě virtuální sítě by měly používat.

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítač a můžete použít bránu NAT, musíme vytvořit virtuální síť.

Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** v **myResourceGroupNAT** pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet).  Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurace služby NAT pro zdrojovou podsíť

Nakonfigurujeme **mySubnet** zdrojové podsítě ve službě Virtual Network **myVnet** tak, aby používala konkrétní prostředek brány NAT **myNATgateway** pomocí [AZ Network VNet Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Tento příkaz aktivuje službu NAT v zadané podsíti.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Veškerý odchozí provoz do internetových cílů teď používá bránu NAT.  Není nutné konfigurovat UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Vytvoření virtuálního počítače pro použití služby NAT

Nyní vytvoříme virtuální počítač pro použití služby NAT.  Tento virtuální počítač má veřejnou IP adresu, která se používá jako veřejná IP adresa na úrovni instance, která umožňuje přístup k virtuálnímu počítači.  Služba NAT má na vědomí směr toku a nahradí výchozí internetový cíl ve vaší podsíti. Veřejná IP adresa virtuálního počítače se nebude používat pro odchozí připojení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup k virtuálnímu počítači.  Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPVM** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Vytvoření NSG pro virtuální počítač

Vzhledem k tomu, že standardní veřejné IP adresy jsou zabezpečené ve výchozím nastavení, musíme vytvořit NSG a povolit tak příchozí přístup pro přístup přes SSH. Pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) vytvořte prostředek NSG s názvem **myNSG** v **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Zveřejnit koncový bod SSH na zdrojovém virtuálním počítači

Vytvoříme pravidlo v NSG pro přístup SSH ke zdrojovému virtuálnímu počítači. Pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) vytvořte pravidlo NSG s názvem **SSH** v NSG s názvem **myNSG** v **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Vytvořit síťovou kartu pro virtuální počítač

Vytvořte síťové rozhraní pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) a přidružte k veřejné IP adrese a skupině zabezpečení sítě. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create).  Vygenerujeme klíče SSH pro tento virtuální počítač a uložíte privátní klíč pro pozdější použití.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Počkejte, až se virtuální počítač nasadí, a pokračujte ve zbývajících krocích.

## <a name="discover-the-ip-address-of-the-vm"></a>Zjištění IP adresy virtuálního počítače

Nejdřív musíme zjistit IP adresu vytvořeného virtuálního počítače. Pokud chcete získat veřejnou IP adresu virtuálního počítače, použijte příkaz [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu počítači.

### <a name="sign-in-to-vm"></a>Přihlášení k virtuálnímu počítači

Přihlašovací údaje SSH by měly být uložené v Cloud Shell z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) v prohlížeči. Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku.

```bash
ssh <ip-address-destination>
```

Nyní jste připraveni použít službu překladu adres (NAT).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili bránu NAT a virtuální počítač pro jeho použití. 

Zkontrolujte metriky v Azure Monitor a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je například vyčerpání prostředků dostupných portů SNAT.  Vyčerpání prostředků SNAT se řeší přidáním dalších prostředků veřejné IP adresy nebo prostředků předpony veřejných IP adres nebo obou.


- Přečtěte si o [službě Azure Virtual Network NAT](./nat-overview.md)
- Přečtěte si o [prostředku brány NAT](./nat-gateway-resource.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

