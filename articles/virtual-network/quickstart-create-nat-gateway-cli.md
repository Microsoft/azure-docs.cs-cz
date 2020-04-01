---
title: 'Úvodní příručka: Vytvoření brány NAT – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Tento rychlý start ukazuje, jak vytvořit bránu NAT pomocí azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 9402960927f56092e226ab81bd3e6ede0cf6a52d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202191"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Úvodní příručka: Vytvoření brány NAT pomocí azure CLI

Tento rychlý start ukazuje, jak používat službu Azure Virtual Network NAT. Vytvoříte bránu NAT, která zajistí odchozí připojení pro virtuální počítač v Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Tento kurz můžete dokončit pomocí Azure Cloud Shell nebo spustit příslušné příkazy místně.  Pokud jste službu Azure Cloud Shell nikdy nepoužívali, [přihlaste](https://shell.azure.com) se a projděte si počáteční nastavení.
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

Chcete-li získat přístup k veřejnému Internetu, potřebujete pro bránu NAT jednu nebo více veřejných IP adres. Pomocí [vytvoření az sítě public-ip vytvořte](https://docs.microsoft.com/cli/azure/network/public-ip) prostředek veřejné IP adresy s názvem **myPublicIP** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Vytvoření veřejné předpony IP

Můžete použít jeden nebo více veřejných prostředků IP adres, veřejné IP předpony nebo obojí s bránou NAT. Do tohoto scénáře přidáme prostředek předpony veřejné IP adresy, který se demonstruje.   Pomocí [předpony veřejné IP adresy az vytvořte](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) veřejný prostředek předpony IP s názvem **myPublicIPprefix** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Vytvoření prostředku brány NAT

V této části je podrobně uvedeno, jak můžete vytvořit a nakonfigurovat následující součásti služby NAT pomocí prostředku brány NAT:
  - Veřejný fond IP adres a veřejná předpona IP pro odchozí toky přeložené prostředkem brány NAT.
  - Změňte časový limit nečinnosti z výchozí hodnoty 4 minut na 10 minut.

Vytvořte globální bránu Azure NAT s [vytvořením brány az network nat](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) s názvem **myNATgateway**. Příkaz používá jak veřejnou IP adresu **myPublicIP,** tak veřejnou IP předponu **myPublicIPprefix**. Příkaz změní časový limit nečinnosti na **10** minut.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

V tomto okamžiku je brána NAT funkční a jediné, co chybí, je konfigurace podsítí virtuální sítě, které by ji měly používat.

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítač a můžete použít bránu NAT, musíme vytvořit virtuální síť.

Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** v **myResourceGroupNAT** pomocí [vytvoření virtuální sítě az](https://docs.microsoft.com/cli/azure/network/vnet).  Adresní prostor IP pro virtuální síť je **192.168.0.0/16**. Podsíť ve virtuální síti je **192.168.0.0/24**.

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

Nakonfigurujeme zdrojovou podsíť **mySubnet** ve virtuální síti **myVnet** tak, aby používala konkrétní prostředek brány NAT **myNATgateway** s [aktualizací podsítě sítě AZ](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Tento příkaz aktivuje službu NAT v zadané podsíti.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Veškerý odchozí provoz do internetových cílů nyní používá bránu NAT.  Není nutné konfigurovat UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Vytvoření virtuálního virtuálního virtuálního provozu pro použití služby NAT

Teď vytvoříme virtuální hod pro použití služby NAT.  Tento virtuální virtuální soud má veřejnou IP adresu, kterou se dá použít jako veřejná IP adresa na úrovni instance, která vám umožní přístup k virtuálnímu virtuálnímu jevu.  Služba NAT je upozorněna na směr toku a nahradí výchozí cíl Internetu v podsíti. Veřejná IP adresa virtuálního počítačů se nepoužije pro odchozí připojení.

### <a name="create-public-ip-for-source-vm"></a>Vytvoření veřejné IP adresy pro zdrojový virtuální virtuální ms

Vytvoříme veřejnou IP adresu, která se použije pro přístup k virtuálnímu virtuálnímu mněmu.  Pomocí [vytvoření az sítě public-ip vytvořte](https://docs.microsoft.com/cli/azure/network/public-ip) prostředek veřejné IP adresy s názvem **myPublicIPVM** v **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Vytvoření nsg pro virtuální ms

Vzhledem k tomu, že standardní veřejné IP adresy jsou ve výchozím nastavení "zabezpečené", musíme vytvořit soubor nsg, který umožní příchozí přístup pro přístup ssh. Pomocí [vytvoření nsg sítě az](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) vytvořte prostředek skupiny nsg s názvem **myNSG** v **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Vystavit koncový bod SSH na zdrojovém virtuálním počítači

Vytvoříme pravidlo v nsg pro přístup SSH ke zdrojovému virtuálnímu virtuálnímu m. Pomocí [vytvoření pravidla nsg sítě az vytvořte pravidlo nsg](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) s názvem **Ssh** v souboru NSG s názvem **myNSG** v **myResourceGroupNAT**.

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

### <a name="create-nic-for-vm"></a>Vytvoření nic onici pro virtuální hod

Vytvořte síťové rozhraní s [az network nic vytvořit](/cli/azure/network/nic#az-network-nic-create) a přidružit k veřejné IP adrese a skupině zabezpečení sítě. 

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

Vytvořte virtuální počítač s [az vm vytvořit](/cli/azure/vm#az-vm-create).  Vygenerujeme klíče ssh pro tento virtuální virtuální ms a uložíme soukromý klíč, který se bude používat později.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Počkejte na nasazení virtuálního virtuálního min a pak pokračujte se zbývajícími kroky.

## <a name="discover-the-ip-address-of-the-vm"></a>Zjištění IP adresy virtuálního soudu

Nejprve musíme zjistit IP adresu virtuálního počítačů, které jste vytvořili. Chcete-li načíst veřejnou IP adresu virtuálního počítačů, použijte [az síť public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Zkopírujte veřejnou IP adresu a vložte ji do poznámkového bloku, abyste ji mohli použít pro přístup k virtuálnímu virtuálnímu okraji.

### <a name="sign-in-to-vm"></a>Přihlášení k virtuálnímu virtuálnímu virtuálnímu montovně

Pověření SSH by měla být uložena ve vašem prostředí Cloud z předchozí operace.  Otevřete [Azure Cloud Shell](https://shell.azure.com) ve svém prohlížeči. Použijte IP adresu načtenou v předchozím kroku na SSH do virtuálního počítače.

```bash
ssh <ip-address-destination>
```

Nyní jste připraveni používat službu NAT.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete použít příkaz [odstranění skupiny az](/cli/azure/group#az-group-delete) k odebrání skupiny prostředků a všech prostředků obsažených v této oblasti.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili bránu NAT a virtuální počítač, abyste ji používali. 

Zkontrolujte metriky v Azure Monitoru a podívejte se, jak vaše služba NAT funguje. Diagnostikujte problémy, jako je vyčerpání prostředků z dostupných portů SNAT.  Vyčerpání prostředků portů SNAT je řešeno přidáním dalších prostředků veřejné IP adresy nebo veřejných prostředků předpony IP nebo obojího.


- Další informace o [virtuální síti Azure NAT](./nat-overview.md)
- Informace o [prostředku brány NAT](./nat-gateway-resource.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí Azure PowerShellu](./quickstart-create-nat-gateway-powershell.md).
- Úvodní příručka pro nasazení [prostředků brány NAT pomocí portálu Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

