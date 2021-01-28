---
title: Vytvoření a otestování brány NAT – Azure CLI
titlesuffix: Azure Virtual Network NAT
description: V tomto kurzu se dozvíte, jak vytvořit bránu NAT pomocí Azure CLI a testovat službu NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 457749b353b9b7fabfb137ebe907463bb17158ba
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98926930"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Kurz: Vytvoření brány NAT pomocí Azure CLI a testování služby NAT

V tomto kurzu vytvoříte bránu NAT, která poskytuje odchozí připojení k virtuálním počítačům v Azure. Pokud chcete otestovat bránu NAT, nasadíte zdrojový a cílový virtuální počítač. Bránu NAT otestujete tak, že vytvoříte odchozí připojení k veřejné IP adrese. Tato připojení budou pocházet ze zdroje do cílového virtuálního počítače. Tento kurz nasadí zdroj a cíl ve dvou různých virtuálních sítích ve stejné skupině prostředků jenom pro zjednodušení.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.71 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2** :

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pro přístup k veřejnému Internetu potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření předpony veřejné IP adresy

Pomocí brány NAT můžete použít jeden nebo více prostředků veřejné IP adresy, předpony veřejných IP adres nebo obojí. Do tohoto scénáře přidáme prostředek předpony veřejných IP adres, který vám ukáže.   Pomocí [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) vytvořte prostředek předpony veřejné IP adresy s názvem **myPublicIPprefixsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

Tato část podrobně popisuje, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a předponu veřejných IP adres, které se mají použít pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minuty na 10 minut.

Vytvořte globální bránu Azure NAT pomocí [AZ Network NAT Gateway Create](/cli/azure/network/nat) s názvem **myNATgateway**. Příkaz používá veřejnou IP adresu **myPublicIP** i předponu veřejné IP adresy **myPublicIPprefix**. Příkaz také změní časový limit nečinnosti na 10 minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

V tomto okamžiku je brána NAT funkční a všechny, které chybí, je konfigurace, které podsítě virtuální sítě by měly používat.

## <a name="prepare-the-source-for-outbound-traffic"></a>Příprava zdroje na odchozí provoz

Provedeme vás instalací kompletního testovacího prostředí. Pomocí Open source nástrojů nastavíte test a ověříte bránu NAT. Začneme se zdrojem, který bude používat bránu NAT, kterou jsme vytvořili dříve.

### <a name="configure-virtual-network-for-source"></a>Konfigurace virtuální sítě pro zdroj

Než nasadíte virtuální počítač a budete moct otestovat bránu NAT, musíme vytvořit virtuální síť.

Vytvořte virtuální síť s názvem **myVnetsource** s podsítí s názvem **mySubnetsource** v **myResourceGroupNAT** pomocí [AZ Network Microsoft Azure Virtual Network Create](/cli/azure/network/vnet).  Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurace služby NAT pro zdrojovou podsíť

Nakonfigurujte zdrojovou podsíť **mySubnetsource** ve službě Virtual Network **myVnetsource** tak, aby používala konkrétní prostředek brány NAT **myNATgateway** pomocí [AZ Network Microsoft Azure Virtual Network podsíti Update](/cli/azure/network/vnet/subnet). Tento příkaz aktivuje službu NAT v zadané podsíti.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

Veškerý odchozí provoz do internetových cílů teď používá službu překladu adres (NAT).  Není nutné konfigurovat UDR.

Než budeme moct otestovat bránu NAT, musíme vytvořit zdrojový virtuální počítač.  Pro přístup k tomuto virtuálnímu počítači z vnějšku přiřadíme prostředek veřejné IP adresy jako veřejnou IP adresu na úrovni instance. Tato adresa se používá jenom pro přístup k tomuto testu.  Ukážeme, jak má služba NAT přednost před jinými odchozími možnostmi.

Tento virtuální počítač můžete také vytvořit bez veřejné IP adresy a vytvořit jiný virtuální počítač pro použití jako JumpBox bez veřejné IP adresy jako cvičení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup ke zdrojovému virtuálnímu počítači. Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPsourceVM** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Vytvoření NSG pro zdrojový virtuální počítač

Vzhledem k tomu, že standardní veřejné IP adresy jsou zabezpečené ve výchozím nastavení, musíme vytvořit NSG a povolit tak příchozí přístup pro přístup přes SSH.  Služba Azure NAT má na vědomí směr toku. Když je ve stejné podsíti nakonfigurovaná brána NAT, NSG se nepoužije pro odchozí připojení. Pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) vytvořte prostředek NSG s názvem **myNSGsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Zveřejnit koncový bod SSH na zdrojovém virtuálním počítači

Vytvoříme pravidlo v NSG pro přístup SSH ke zdrojovému virtuálnímu počítači. Pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) vytvořte pravidlo NSG s názvem **SSH**. Toto pravidlo se vytvoří v NSG s názvem **myNSGsource** ve skupině prostředků **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Vytvořit síťovou kartu pro zdrojový virtuální počítač

Vytvořte síťové rozhraní pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) a přidružte k veřejné IP adrese a skupině zabezpečení sítě. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Vytvoření zdrojového virtuálního počítače

Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create).  Vygenerujeme klíče SSH pro tento virtuální počítač a uložíte privátní klíč pro pozdější použití.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

I když se příkaz vrátí hned, může trvat několik minut, než se virtuální počítač nasadí.

## <a name="prepare-destination-for-outbound-traffic"></a>Příprava cíle pro odchozí provoz

Nyní vytvoříme cíl pro odchozí přenosy přeložené službou překladu adres (NAT), abyste je mohli otestovat.

### <a name="configure-virtual-network-for-destination"></a>Konfigurace virtuální sítě pro cíl

 Musíme vytvořit virtuální síť, ve které bude cílový virtuální počítač.  Tyto příkazy jsou stejný postup jako u zdrojového virtuálního počítače s malými změnami k zpřístupnění cílového koncového bodu.

Vytvořte virtuální síť s názvem **myVnetdestination** s podsítí s názvem **mySubnetdestination** v **myResourceGroupNAT** pomocí [AZ Network Microsoft Azure Virtual Network Create](/cli/azure/network/vnet).  Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Vytvoření veřejné IP adresy pro cílový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup ke zdrojovému virtuálnímu počítači. Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPdestinationVM** v **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Vytvoření NSG pro cílový virtuální počítač

Standardní veřejné IP adresy jsou "zabezpečené ve výchozím nastavení", budete muset vytvořit NSG a povolit tak příchozí přístup pro SSH. Služba Azure NAT má na vědomí směr toku. Když je ve stejné podsíti nakonfigurovaná brána NAT, NSG se nepoužije pro odchozí připojení. Pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) vytvořte prostředek NSG s názvem **myNSGdestination** v **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Vystavení koncového bodu SSH na cílovém virtuálním počítači

Vytvoříme pravidlo v NSG pro přístup SSH k cílovému virtuálnímu počítači. Pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) vytvořte pravidlo NSG s názvem **SSH**. Toto pravidlo se vytvoří v NSG s názvem **myNSGdestination** ve skupině prostředků **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Vystavit koncový bod HTTP na cílovém virtuálním počítači

Vytvoříme pravidlo v NSG pro přístup HTTP k cílovému virtuálnímu počítači. Pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) vytvořte pravidlo NSG s názvem **http** v NSG s názvem **myNSGdestination** v **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Vytvořit síťovou kartu pro cílový virtuální počítač

Vytvořte síťové rozhraní pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) a přidružte se k veřejné IP adrese **myPublicIPdestinationVM** a skupině zabezpečení sítě **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Vytvoření cílového virtuálního počítače

Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az-vm-create).  Vygenerujeme klíče SSH pro tento virtuální počítač a uložíte privátní klíč pro pozdější použití.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
I když se příkaz vrátí hned, může trvat několik minut, než se virtuální počítač nasadí.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Příprava webového serveru a datové části testu na cílovém virtuálním počítači

Nejdřív musíme zjistit IP adresu cílového virtuálního počítače.  Pokud chcete získat veřejnou IP adresu cílového virtuálního počítače, použijte příkaz [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označuje, že se jedná o cílový virtuální počítač.

### <a name="sign-in-to-destination-vm"></a>Přihlášení k cílovému virtuálnímu počítači

Přihlašovací údaje SSH by měly být uložené v Cloud Shell z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) v prohlížeči. Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku. 

```bash
ssh <ip-address-destination>
```

Po přihlášení zkopírujte a vložte následující příkazy.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Tyto příkazy aktualizují váš virtuální počítač, nainstaluje Nginx a vytvoří soubor 100-kilobajtů. Tento soubor se načte ze zdrojového virtuálního počítače pomocí služby NAT.

Zavřete relaci SSH s cílovým virtuálním počítačem.

## <a name="prepare-test-on-source-vm"></a>Příprava testu na zdrojovém virtuálním počítači

Nejdřív musíme zjistit IP adresu zdrojového virtuálního počítače.  Pokud chcete získat veřejnou IP adresu zdrojového virtuálního počítače, použijte příkaz [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označuje, že se jedná o zdrojový virtuální počítač.

### <a name="sign-in-to-source-vm"></a>Přihlášení ke zdrojovému virtuálnímu počítači

Přihlašovací údaje SSH se znovu ukládají v Cloud Shell. Otevře novou kartu pro [Azure Cloud Shell](https://shell.azure.com) v prohlížeči.  Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku. 

```bash
ssh <ip-address-source>
```

Zkopírujte a vložte následující příkazy, které se připraví na testování služby překladu adres (NAT).

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Tento příkaz aktualizuje váš virtuální počítač, nainstaluje se, nainstaluje [Hey](https://github.com/rakyll/hey) z GitHubu a aktualizuje prostředí prostředí.

Nyní jste připraveni otestovat službu NAT.

## <a name="validate-nat-service"></a>Ověřit službu NAT

Během přihlášení ke zdrojovému virtuálnímu počítači můžete k vygenerování požadavků na cílovou IP adresu použít **oblé** a **Hey** .

Pomocí oblé načtěte soubor 100-kilobajtů.  **\<ip-address-destination>** V následujícím příkladu nahraďte cílovou IP adresou, kterou jste zkopírovali dříve.  Parametr **--Output** označuje, že načtený soubor bude zahozen.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Můžete také vygenerovat sérii požadavků pomocí **Hey**. Znovu nahraďte **\<ip-address-destination>** cílovou IP adresou, kterou jste zkopírovali dříve.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Tento příkaz vygeneruje 100 požadavků, 10 současně s časovým limitem 30 sekund. Připojení TCP se nebude znovu používat.  Každý požadavek získá 100 kB.  Na konci běhu ohlásí funkce **Hey** informace o tom, jak dobře služba NAT provedla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili bránu NAT, vytvořili zdrojový a cílový virtuální počítač a potom jste otestovali bránu NAT.

Zkontrolujte metriky v Azure Monitor a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je například vyčerpání prostředků dostupných portů SNAT.  Vyčerpání prostředků na portech SNAT se dá snadno adresovat přidáním dalších prostředků veřejné IP adresy nebo prostředků předpony veřejných IP adres nebo obou.

- Informace o [Virtual Network NAT](./nat-overview.md)
- Přečtěte si o [prostředku brány NAT](./nat-gateway-resource.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Rychlý Start pro nasazení [prostředku brány NAT pomocí Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]
