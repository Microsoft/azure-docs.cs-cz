---
title: 'Kurz: Vytvoření a testování brány NAT – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Tento kurz ukazuje, jak vytvořit bránu NAT pomocí azure CLI a otestovat službu NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: b3e10b3abbe5c9815e51ce67786882dbd294df3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202208"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Kurz: Vytvoření brány NAT pomocí azure CLI a testování služby NAT

V tomto kurzu vytvoříte bránu NAT, která zajistí odchozí připojení pro virtuální počítače v Azure. Chcete-li otestovat bránu NAT, nasadíte zdrojový a cílový virtuální počítač. Bránu NAT otestujete vytvořením odchozích připojení k veřejné IP adrese. Tato připojení budou pocházet ze zdroje do cílového virtuálního počítače. Tento kurz nasazuje zdroj a cíl ve dvou různých virtuálních sítích ve stejné skupině prostředků pouze pro jednoduchost.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento kurz můžete dokončit pomocí Azure Cloud Shell nebo spustit příslušné příkazy místně.  Pokud jste Azure Cloud Shell nepoužili, měli byste [se přihlásit.](https://shell.azure.com)

Pokud se rozhodnete spustit tyto příkazy místně, je třeba nainstalovat příkaz cli.  Tento kurz vyžaduje, abyste spouštěli verzi Azure CLI verze 2.0.71 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroupNAT** v umístění **eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Vytvoření brány NAT

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Chcete-li získat přístup k veřejnému Internetu, potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [vytvoření az sítě public-ip vytvořte](https://docs.microsoft.com/cli/azure/network/public-ip) prostředek veřejné IP adresy s názvem **myPublicIPsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření veřejné předpony IP

Můžete použít jeden nebo více veřejných prostředků IP adres, veřejné IP předpony nebo obojí s bránou NAT. Do tohoto scénáře přidáme prostředek veřejné předpony IP, který se demonstruje.   Pomocí [předpony veřejné IP sítě az vytvořte](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) veřejný prostředek předpony IP s názvem **myPublicIPprefixsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

V této části je podrobně uvedeno, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a veřejná předpona IP pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minut na 10 minut.

Vytvořte globální bránu Azure NAT s [vytvořením brány az network nat](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) s názvem **myNATgateway**. Příkaz používá jak veřejnou IP adresu **myPublicIP,** tak veřejnou IP předponu **myPublicIPprefix**. Příkaz také změní časový limit nečinnosti na 10 minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

V tomto okamžiku je brána NAT funkční a jediné, co chybí, je konfigurace podsítí virtuální sítě, které by ji měly používat.

## <a name="prepare-the-source-for-outbound-traffic"></a>Příprava zdroje pro odchozí provoz

Provedeme vás nastavením úplného testovacího prostředí. K ověření brány NAT nastavíte test pomocí nástrojů s otevřeným zdrojovým kódem. Začneme se zdrojem, který bude používat bránu NAT, kterou jsme vytvořili dříve.

### <a name="configure-virtual-network-for-source"></a>Konfigurace virtuální sítě pro zdroj

Než nasadíte virtuální počítač a můžete otestovat bránu NAT, musíme vytvořit virtuální síť.

Vytvořte virtuální síť s názvem **myVnetsource** s podsítí s názvem **mySubnetsource** v **myResourceGroupNAT** pomocí [az sítě Microsoft Azure Virtual Network vytvořit](https://docs.microsoft.com/cli/azure/network/vnet).  Adresní prostor IP pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.

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

Nakonfigurujte zdrojovou podsíť **mySubnetsource** ve virtuální síti **myVnetsource** tak, aby používala konkrétní prostředek brány NAT **myNATgateway** s [aktualizací podsítě Microsoft Azure Virtual Network](https://docs.microsoft.com/cli/azure/network/vnet/subnet). Tento příkaz aktivuje službu NAT v zadané podsíti.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

Veškerý odchozí provoz do internetových cílů nyní používá službu NAT.  Není nutné konfigurovat UDR.

Než budeme moci otestovat bránu NAT, musíme vytvořit zdrojový virtuální počítač.  Přiřadíme prostředek veřejné IP adresy jako veřejnou IP adresu na úrovni instance pro přístup k tomuto virtuálnímu virtuálnímu jevu zvenčí. Tato adresa se používá pouze pro přístup k ní pro test.  Ukážeme, jak má služba NAT přednost před ostatními odchozími možnostmi.

Tento virtuální virtuální ms můžete také vytvořit bez veřejné IP adresy a vytvořit jiný virtuální virtuální virtuální< systém, který se použije jako jumpbox bez veřejné IP adresy jako cvičení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální virtuální ms

Vytvoříme veřejnou IP adresu, která se použije pro přístup ke zdrojovému virtuálnímu virtuálnímu počítačům. Pomocí [vytvoření az sítě public-ip vytvořte](https://docs.microsoft.com/cli/azure/network/public-ip) prostředek veřejné IP adresy s názvem **myPublicIPsourceVM** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Vytvoření nsg pro zdrojový virtuální virtuální ms

Vzhledem k tomu, že standardní veřejné IP adresy jsou ve výchozím nastavení "zabezpečené", musíme vytvořit soubor nsg, který umožní příchozí přístup pro přístup ssh.  Služba Azure NAT je vědomá směru toku. Tento soubor nsg nebude použit pro odchozí po nakonfigurován bránu NAT ve stejné podsíti. Pomocí [vytvoření nsg sítě az](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) vytvořte prostředek skupiny nsg s názvem **myNSGsource** v **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Vystavit koncový bod SSH na zdrojovém virtuálním počítači

Vytvoříme pravidlo v nsg pro přístup SSH ke zdrojovému virtuálnímu virtuálnímu m. Pomocí [vytvoření pravidla nsg sítě az vytvořte](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) pravidlo nsg s názvem **Ssh**. Toto pravidlo bude vytvořeno v souboru nsg s názvem **myNSGsource** ve skupině prostředků **myResourceGroupNAT**.

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

### <a name="create-nic-for-source-vm"></a>Vytvoření nic pro zdrojový virtuální virtuální ms

Vytvořte síťové rozhraní s [az network nic vytvořit](/cli/azure/network/nic#az-network-nic-create) a přidružit k veřejné IP adrese a skupině zabezpečení sítě. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Vytvoření zdrojového virtuálního virtuálního montova

Vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az-vm-create).  Vygenerujeme klíče ssh pro tento virtuální virtuální ms a uložíme soukromý klíč, který se bude používat později.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Zatímco příkaz se vrátí okamžitě, může trvat několik minut pro virtuální ho nasazení.

## <a name="prepare-destination-for-outbound-traffic"></a>Příprava cíle pro odchozí provoz

Nyní vytvoříme cíl pro odchozí provoz přeložený službou NAT, abyste jej mohli otestovat.

### <a name="configure-virtual-network-for-destination"></a>Konfigurace virtuální sítě pro cíl

 Musíme vytvořit virtuální síť, kde bude cílový virtuální počítač.  Tyto příkazy jsou stejné kroky jako pro zdrojový virtuální virtuální ms s malými změnami vystavit cílový koncový bod.

Vytvořte virtuální síť s názvem **myVnetdestination** s podsítí s názvem **mySubnetdestination** v **myResourceGroupNAT** pomocí [vytvoření az sítě Microsoft Azure Virtual Network](https://docs.microsoft.com/cli/azure/network/vnet).  Adresní prostor IP pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Vytvoření veřejné IP adresy pro cílový virtuální virtuální ms

Vytvoříme veřejnou IP adresu, která se použije pro přístup ke zdrojovému virtuálnímu virtuálnímu počítačům. Pomocí [vytvoření az sítě public-ip vytvořte](https://docs.microsoft.com/cli/azure/network/public-ip) prostředek veřejné IP adresy s názvem **myPublicIPdestinationVM** v **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Vytvoření nsg pro cílový virtuální virtuální ms

Standardní veřejné IP adresy jsou 'bezpečné ve výchozím nastavení', budete muset vytvořit nsg povolit příchozí přístup pro ssh. Služba Azure NAT je vědoma směru toku. Tento soubor nsg nebude použit pro odchozí po nakonfigurován bránu NAT ve stejné podsíti. Pomocí [vytvoření nsg sítě az](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) vytvořte prostředek skupiny nsg s názvem **myNSGdestination** v **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Vystavit koncový bod SSH na cílovém virtuálním počítači

Vytvoříme pravidlo v nsg pro přístup SSH k cílovému virtuálnímu virtuálnímu míse. Pomocí [vytvoření pravidla nsg sítě az vytvořte](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) pravidlo nsg s názvem **Ssh**. Toto pravidlo bude vytvořeno v souboru nsg s názvem **myNSGdestination** ve skupině prostředků **myResourceGroupNAT**.

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

Vytvoříme pravidlo v souboru nsg pro přístup HTTP k cílovému virtuálnímu virtuálnímu montovně. Pomocí [vytvoření pravidla nsg sítě az vytvořte](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) pravidlo nsg s názvem **http** v souboru NSG s názvem **myNSGdestination** v **myResourceGroupNAT**.

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

### <a name="create-nic-for-destination-vm"></a>Vytvořit nic pro cílový virtuální ms

Vytvořte síťové rozhraní s [az network nic vytvořit](/cli/azure/network/nic#az-network-nic-create) a přidružit k veřejné IP adrese **myPublicIPdestinationVM** a skupině zabezpečení sítě **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>Vytvoření cílového virtuálního virtuálního montova

Vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az-vm-create).  Vygenerujeme klíče ssh pro tento virtuální virtuální ms a uložíme soukromý klíč, který se bude používat později.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
Zatímco příkaz se vrátí okamžitě, může trvat několik minut pro virtuální ho nasazení.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Příprava webového serveru a testování datové části na cílovém virtuálním počítači

Nejprve musíme zjistit IP adresu cílového virtuálního počítačů.  Chcete-li získat veřejnou IP adresu cílového virtuálního počítačů, použijte [az síť public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označte, že se jedná o cílový virtuální počítač.

### <a name="sign-in-to-destination-vm"></a>Přihlášení k cílovému virtuálnímu virtuálnímu mísu

Pověření SSH by měla být uložena ve vašem prostředí Cloud z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) ve svém prohlížeči. Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače. 

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

Tyto příkazy aktualizují váš virtuální počítač, nainstalují nginx a vytvoří soubor 100 KBytes. Tento soubor bude načten ze zdrojového virtuálního virtuálního aplikace pomocí služby NAT.

Zavřete relaci SSH s cílovým virtuálním virtuálním mem.

## <a name="prepare-test-on-source-vm"></a>Příprava testu na zdrojovém virtuálním počítači

Nejprve musíme zjistit IP adresu zdrojového virtuálního počítačů.  Chcete-li získat veřejnou IP adresu zdrojového virtuálního virtuálního počítačů, použijte [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít v následujících krocích. Označte, že se jedná o zdrojový virtuální počítač.

### <a name="sign-in-to-source-vm"></a>Přihlášení ke zdrojovému virtuálnímu virtuálnímu virtuálnímu virtuálnímu montovně

Pověření SSH jsou opět uloženy v prostředí Cloud Shell. Otevřete novou kartu pro [Azure Cloud Shell](https://shell.azure.com) ve vašem prohlížeči.  Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače. 

```bash
ssh <ip-address-source>
```

Zkopírujte a vložte následující příkazy a připravte se na testování služby NAT.

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

Tento příkaz aktualizuje váš virtuální počítač, nainstaluje go, nainstaluje hej z [GitHubu](https://github.com/rakyll/hey) a aktualizuje prostředí prostředí prostředí.

Nyní jste připraveni otestovat službu NAT.

## <a name="validate-nat-service"></a>Ověřit službu NAT

Při přihlášení ke zdrojovému virtuálnímu virtuálnímu počítačůmu můžete ke generování požadavků na cílovou IP adresu použít **curl** a **hey.**

Pomocí zvlnění načtěte soubor 100 KBytes.  Nahraďte ** \<cílovou>ip-adres** v níže uvedeném příkladu cílovou adresou IP, kterou jste dříve zkopírovali.  Parametr **--output** označuje, že načtený soubor bude zahozen.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Můžete také generovat řadu požadavků pomocí **hey**. Opět nahraďte ** \<cílovou>ip-address>** cílovou ADRESOu IP, kterou jste dříve zkopírovali.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Tento příkaz vygeneruje 100 požadavků, 10 současně, s časovým časem 30 sekund. Připojení TCP nebude znovu použito.  Každý požadavek načte 100 kbajtů.  Na konci běhu, **bude hej** zprávu některé statistiky o tom, jak dobře na vychýlivá služba NAT.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete použít příkaz [odstranění skupiny az](/cli/azure/group#az-group-delete) k odebrání skupiny prostředků a všech prostředků obsažených v této oblasti.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili bránu NAT, vytvořili zdrojový a cílový virtuální počítač a pak otestovali bránu NAT.

Zkontrolujte metriky v Azure Monitoru a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je vyčerpání prostředků z dostupných portů SNAT.  Vyčerpání prostředků portů SNAT lze snadno vyřešit přidáním dalších prostředků veřejné IP adresy nebo veřejných prostředků předpony IP nebo obojího.

- Další informace o [překladu virtuálních sítí](./nat-overview.md)
- Informace o [prostředku brány NAT](./nat-gateway-resource.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure PowerShellu](./quickstart-create-nat-gateway-powershell.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí portálu Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

