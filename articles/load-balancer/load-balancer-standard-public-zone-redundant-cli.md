---
title: Zóna vyrovnávání zatížení – redundantní virtuální počítače pomocí Azure CLI
titleSuffix: Azure Load Balancer
description: Naučte se vytvářet veřejné Standard Load Balancer s zónou redundantního front-endu pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: allensu
ms.openlocfilehash: c33c1efd735a9c606ebe4625eb704005fff64a9e
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896068"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Vyrovnávání zatížení virtuálních počítačů napříč všemi zónami dostupnosti pomocí Azure CLI

Tento článek vás provede vytvořením veřejné [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) s koncovým front-redundantním frontou pro zajištění redundance zóny bez závislosti na více záznamech DNS. Jedna front-end IP adresa je automaticky redundantní pro zónu.  Pomocí zóny redundantního front-endu pro váš nástroj pro vyrovnávání zatížení s jednou IP adresou teď můžete získat přístup k libovolnému virtuálnímu počítači ve virtuální síti v rámci oblasti, která je ve všech Zóny dostupnostich. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Další informace o používání zón dostupnosti s Load Balancerem úrovně Standard najdete v tématu o [Load Balanceru úrovně Standard a zónách dostupnosti](load-balancer-standard-availability-zones.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěný Azure CLI verze 2.0.17 nebo novější.  Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSLB* v umístění *westeurope* :

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Vytvoření standardu veřejné IP adresy zóny redundantní
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Záložní front-end zóna je obsluhována všemi zónami dostupnosti v oblasti současně. Vytvořte zónu redundantní veřejné IP adresy pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create). Když vytvoříte standardní veřejnou IP adresu, bude ve výchozím nastavení zóna redundantní.

Následující příklad vytvoří zónu redundantní veřejnou IP adresu s názvem *myPublicIP* ve skupině prostředků *myResourceGroupLoadBalancer* .

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Vytvoření Standard Load Balancer Azure
Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
- Front-endový fond IP adres, který přijímá příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
- Back-endový fond IP adres, kam front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.
- Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
- Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Vytvořte standardní nástroj pro vyrovnávání zatížení pomocí [AZ Network](/cli/azure/network/lb#az-network-lb-create)diskont Create. Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem *myLoadBalancer* a přiřadí *myPublicIP* adresu konfigurace front-endové IP adresy.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Vytvoření sondy stavu na portu 80

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Vytvořte sondu stavu pomocí AZ Network disprobe Create a Monitorujte stav virtuálních počítačů. Sondu stavu protokolu TCP vytvoříte pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Následující příklad vytvoří sondu stavu *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení pro port 80
Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Pomocí příkazu *myLoadBalancerRuleWeb* vytvořte pravidlo nástroje pro vyrovnávání zatížení [myLoadBalancerRuleWeb](/cli/azure/network/lb/rule#az-network-lb-rule-create) pro naslouchání na portu 80 ve front-endovém fondu *myFrontEndPool* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool* rovněž na portu 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s názvem *myVnet* s podsítí s názvem *mySubnet* v myResourceGroup pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* , která definuje příchozí připojení k virtuální síti pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Vytvořte pravidlo skupiny zabezpečení sítě s názvem *myNetworkSecurityGroupRule* pro port 80 pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>Vytvoření síťových rozhraní
Vytvořte tři virtuální síťové karty pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) a přidružte je k veřejné IP adrese a skupině zabezpečení sítě. Následující příklad vytvoří šest virtuálních síťových karet. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Vytvoření serverů back-end
V tomto příkladu vytvoříte tři virtuální počítače umístěné v zóně 1, v zóně 2 a v zóně 3 jako servery back-end pro nástroj pro vyrovnávání zatížení. Na virtuální počítače nainstalujete také NGINX a ověříte tak, že nástroj pro vyrovnávání zatížení byl úspěšně vytvořen.

### <a name="create-cloud-init-config"></a>Vytvoření konfigurace cloud-init

K instalaci serveru NGINX a spuštění aplikace Hello World v Node.js na virtuálním počítači s Linuxem můžete použít konfigurační soubor cloud-init. V aktuálním prostředí vytvořte soubor cloud-init.txt a zkopírujte následující konfiguraci a vložte ji do prostředí. Ujistěte se, že správně kopírujete celý soubor cloud-init, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-the-zonal-virtual-machines"></a>Vytvoření virtuálních počítačů s více oblastmi
Virtuální počítače vytvoříte pomocí [AZ VM Create](/cli/azure/vm#az-vm-create) v zóně 1, zóně 2 a zóně 3. Následující příklad vytvoří virtuální počítač v každé zóně a vygeneruje klíče SSH, pokud ještě neexistují:

Vytvořte virtuální počítač v každé zóně (zóna 1, zóna 2 a zóna 3) umístění *westeurope* .

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Získejte veřejnou IP adresu nástroje pro vyrovnávání zatížení pomocí [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Nezapomeňte, že příprava virtuálních počítačů několik minut trvá, a až pak do nich začne nástroj pro vyrovnávání zatížení distribuovat provoz. Zobrazí se aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštěná aplikace Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Pokud chcete vidět, že nástroj pro vyrovnávání zatížení distribuuje provoz mezi virtuálními počítači ve všech třech zónách dostupnosti, na kterých běží vaše aplikace, můžete zastavit virtuální počítač v konkrétní zóně a aktualizovat si prohlížeč.

## <a name="next-steps"></a>Další kroky
- Další informace o [Load Balanceru úrovně Standard](./load-balancer-standard-overview.md)



