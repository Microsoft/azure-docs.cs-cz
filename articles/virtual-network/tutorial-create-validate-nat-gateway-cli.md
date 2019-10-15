---
title: 'Kurz: vytvoření a otestování brány NAT – Azure CLI'
titlesuffix: Azure NAT service
description: V tomto kurzu se dozvíte, jak vytvořit bránu NAT pomocí Azure CLI a testovat službu NAT.
services: nat
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.custom: seodec18
ms.service: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: f97284b3c629dbaab1b0cbb54b1f114342278da7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376487"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Kurz: Vytvoření brány NAT pomocí Azure CLI a testování služby NAT

V tomto kurzu se dozvíte, jak používat službu Azure NAT a vytvořit bránu NAT, která poskytuje odchozí připojení k virtuálním počítačům v Azure. Pokud chcete otestovat bránu NAT, nasadíte zdrojový a cílový virtuální počítač.  Bránu NAT otestujete tak, že odchozí připojení k veřejné IP adrese ze zdroje nasadíte do cílového virtuálního počítače.  Tento kurz nasadí zdroj a cíl ve dvou různých virtuálních sítích ve stejné skupině prostředků jenom pro zjednodušení.

>[!NOTE] 
>Služba Azure NAT je v tuto chvíli dostupná jako Public Preview a je dostupná v omezené sadě [oblastí](https://azure.microsoft.com/global-infrastructure/regions/). Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento kurz můžete dokončit pomocí Azure Cloud shellu nebo místně spustit příslušné příkazy.  Pokud jste službu Azure Cloud Shell nikdy nepoužívali, měli byste se [Přihlásit](https://shell.azure.com) , abyste mohli projít úvodním nastavením.

Pokud se rozhodnete tyto příkazy spustit lokálně, musíte nainstalovat rozhraní příkazového řádku.  Tento kurz vyžaduje, abyste spustili verzi Azure CLI verze 2.0.71 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

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

Pro přístup k veřejnému Internetu potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření předpony veřejné IP adresy

Pomocí brány NAT můžete použít jeden nebo víc prostředků veřejné IP adresy nebo jednu nebo víc IP adres veřejných IP adres nebo obojí. Do tohoto scénáře přidáme prostředek předpony veřejných IP adres, který vám ukáže.   Pomocí [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip-prefix) vytvořte prostředek předpony veřejné IP adresy s názvem **myPublicIPprefixsource** v **myResourceGroupNAT**.

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

Vytvořte globální bránu Azure NAT pomocí [AZ Network NAT Gateway Create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) s názvem **myNATgateway** , která používá jak veřejnou IP adresu **myPublicIPsource** , tak **myPublicIPprefixsource** předponu veřejné IP adresy a změní časový limit nečinnosti na. **10 minut**.

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

Provedeme vás konfigurací kompletního testovacího prostředí a provedením testů v dalších krocích. Začneme se zdrojem, který bude používat prostředek brány NAT, který jsme vytvořili dříve.

### <a name="configure-virtual-network-for-source"></a>Konfigurace virtuální sítě pro zdroj

Než nasadíte virtuální počítač a budete moct otestovat bránu NAT, musíme vytvořit virtuální síť.

Vytvořte virtuální síť s názvem **myVnetsource** s podsítí s názvem **mySubnetsource** v **myResourceGroupNAT** pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet).  Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16** a podsíť ve virtuální síti je **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurace služby NAT pro zdrojovou podsíť

Už jsme vytvořili bránu NAT a teď nakonfigurovali zdrojovou podsíť **mySubnetsource** ve službě Virtual Network **myVnetsource** tak, aby používala konkrétní prostředek brány NAT **myNATsource** pomocí [AZ Network VNet Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Tento příkaz aktivuje službu NAT v zadané podsíti.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Veškerý odchozí provoz do internetových cílů teď používá službu překladu adres (NAT).  Není nutné konfigurovat UDR.

Než budeme moct otestovat bránu NAT, musíme vytvořit zdrojový virtuální počítač.  Prostředek veřejné IP adresy přiřadíme jako veřejnou IP adresu na úrovni instance pro přístup k tomuto virtuálnímu počítači z vnějšku. Tato adresa se používá jenom pro přístup k tomuto testu.  Ukážeme, jak má služba NAT přednost před jinými odchozími možnostmi.

Tento virtuální počítač můžete také vytvořit bez veřejné IP adresy a vytvořit jiný virtuální počítač pro použití jako JumpBox bez veřejné IP adresy jako cvičení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup ke zdrojovému virtuálnímu počítači. Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPsourceVM** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Vytvoření NSG pro zdrojový virtuální počítač

Vzhledem k tomu, že standardní veřejné IP adresy jsou zabezpečené ve výchozím nastavení, musíme vytvořit NSG a povolit tak příchozí přístup pro přístup přes SSH.  Vzhledem k tomu, že služba NAT má na vědomí směr toku, tento NSG se nepoužije pro odchozí připojení, když je ve stejné podsíti nakonfigurovaná brána NAT. Pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) vytvořte prostředek NSG s názvem **myNSGsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Zveřejnit koncový bod SSH na zdrojovém virtuálním počítači

Vytvoříme pravidlo v NSG pro přístup SSH ke zdrojovému virtuálnímu počítači. Pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) vytvořte pravidlo NSG s názvem **SSH** v NSG s názvem **myNSGsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
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
    --public-ip-address myPublicIPSourceVM
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

Než nasadíte virtuální počítač a pro cíl, musíme také vytvořit virtuální síť, ve které je možné umístit cílový virtuální počítač.  Jedná se o stejný postup jako u zdrojového virtuálního počítače s nějakými malými změnami k vystavení cílového koncového bodu.

Vytvořte virtuální síť s názvem **myVnetdestination** s podsítí s názvem **mySubnetdestination** v **myResourceGroupNAT** pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet).  Adresní prostor IP adres pro virtuální síť je **192.168.0.0/16** a podsíť ve virtuální síti je **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Vytvoření veřejné IP adresy pro cílový virtuální počítač

Vytvoříme veřejnou IP adresu, která se použije pro přístup ke zdrojovému virtuálnímu počítači. Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip) vytvořte prostředek veřejné IP adresy s názvem **myPublicIPdestinationVM** v **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM
  --sku standard
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Vytvoření NSG pro cílový virtuální počítač

Vzhledem k tomu, že standardní veřejné IP adresy jsou zabezpečené ve výchozím nastavení, musíme vytvořit NSG a povolit tak příchozí přístup pro přístup přes SSH.  Vzhledem k tomu, že služba NAT má na vědomí směr toku, tento NSG se nepoužije pro odchozí připojení, když je ve stejné podsíti nakonfigurovaná brána NAT. Pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) vytvořte prostředek NSG s názvem **myNSGdestination** v **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Vystavení koncového bodu SSH na cílovém virtuálním počítači

Vytvoříme pravidlo v NSG pro přístup SSH k cílovému virtuálnímu počítači. Pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) vytvořte pravidlo NSG s názvem **SSH** v NSG s názvem **myNSGdestination** v **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow 
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Vystavit koncový bod HTTP na cílovém virtuálním počítači

Vytvoříme pravidlo v NSG pro přístup HTTP k cílovému virtuálnímu počítači. Pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) vytvořte pravidlo NSG s názvem **http** v NSG s názvem **myNSGdestination** v **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow 
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
    --public-ip-address myPublicIPdestinationVM
    --network-security-group myNSGdestination
    --location westus
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
    --location westus
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

Přihlašovací údaje SSH by měly být uložené v Cloud shellu z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) v prohlížeči. Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku. 

```bash
ssh <ip-address-destination>
```

Po přihlášení zkopírujte a vložte následující příkazy.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Tyto příkazy aktualizují váš virtuální počítač, nainstaluje Nginx a vytvoří soubor 100 kilobajtů, který můžete použít k načtení ze zdrojového virtuálního počítače pomocí služby překladu adres (NAT).

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

### <a name="log-into-source-vm"></a>Přihlášení ke zdrojovému virtuálnímu počítači

Přihlašovací údaje SSH se znovu ukládají ve službě cloud Shell. Otevře novou kartu pro [Azure Cloud Shell](https://shell.azure.com) v prohlížeči.  Pro SSH k virtuálnímu počítači použijte IP adresu získanou v předchozím kroku. 

```bash
ssh <ip-address-source>
```

Zkopírujte a vložte následující příkazy, které se připraví na testování služby překladu adres (NAT).

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Tím se aktualizuje váš virtuální počítač, nainstaluje se, nainstaluje se [Hey](https://github.com/rakyll/hey) z GitHubu a aktualizuje se prostředí prostředí.

Nyní jste připraveni k testování služby NAT.

## <a name="validate-nat-service"></a>Ověřit službu NAT

Během přihlášení ke zdrojovému virtuálnímu počítači můžete k vygenerování požadavků na cílovou IP adresu použít **oblé** a **Hey** .

Pomocí oblé načtěte soubor 100 kilobajtů.  V následujícím příkladu nahraďte **\<ip-Address->** cílovou IP adresou, kterou jste dříve zkopírovali.  Parametr **--Output** označuje, že načtený soubor bude zahozen.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Můžete také vygenerovat sérii požadavků pomocí **Hey**. Znovu nahraďte **\<ip-Address-destination >** cílovou IP adresou, kterou jste dříve zkopírovali.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Tím se vygeneruje 100 požadavků, 10 současně s časovým limitem 30 sekund a bez opakovaného použití připojení TCP.  Každý požadavek získá 100 kB.  Na konci běhu ohlásí funkce **Hey** informace o tom, jak dobře služba NAT provedla.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků obsažených v nástroji použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili bránu NAT, vytvořili zdrojový a cílový virtuální počítač a potom jste otestovali bránu NAT. Další informace o službě Azure NAT najdete v dalších kurzech pro službu Azure NAT.

Můžete také zkontrolovat metriky v Azure Monitor, abyste viděli, jak vaše služba NAT pracuje, a diagnostikovat problémy, jako je například vyčerpání prostředků dostupných portů SNAT.  Vyčerpání prostředků na portech SNAT se dá snadno adresovat přidáním dalších prostředků veřejné IP adresy nebo prostředků předpony veřejných IP adres nebo obou.

> [!div class="nextstepaction"]

