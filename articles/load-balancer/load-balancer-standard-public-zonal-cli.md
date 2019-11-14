---
title: Standard Load Balancer s front-endy veřejné IP adresy – Azure CLI
titleSuffix: Azure Load Balancer
description: Naučte se vytvářet veřejné Standard Load Balancer s front-endy veřejné IP adresy front-endu pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: b9c3a88df6801566bc927cfc18fda0adfa05a5ae
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076033"
---
#  <a name="create-a-standard-load-balancer-with-zonal-public-ip-address-frontend-using-azure-cli"></a>Vytvoření Standard Load Balancer pomocí Azure CLI pomocí veřejné IP adresy pro oblast front-endu

Tento článek vás provede vytvořením veřejné [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) s Zona front-end s použitím standardní IP adresy veřejné IP adresy. V tomto scénáři zadáte určitou zónu pro instance front-endu a back-endu, aby se cesta k datům a prostředky spojily s konkrétní zónou.

Další informace o používání zón dostupnosti s Load Balancerem úrovně Standard najdete v tématu o [Load Balanceru úrovně Standard a zónách dostupnosti](load-balancer-standard-availability-zones.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, ujistěte se, že máte nainstalovanou nejnovější verzi [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a přihlášení k účtu Azure pomocí [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
>  Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupLB* v umístění *westeurope* :

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Vytvoření standardu veřejné IP adresy Zona
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Veřejná IP adresa, která je vytvořena v konkrétní zóně, bude vždy existovat pouze v této zóně. Zónu veřejné IP adresy není možné změnit.

Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). Následující příklad vytvoří veřejnou IP adresu Zona s názvem *myPublicIP* ve skupině prostředků *myResourceGroupLoadBalancer* v zóně 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-standard-load-balancer"></a>Vytvoření Standard Load Balancer Azure
Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
- Front-endový fond IP adres, který přijímá příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
- Back-endový fond IP adres, kam front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.
- Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
- Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Vytvořte Standard Load Balancer pomocí [AZ Network](/cli/azure/network/lb#az-network-lb-create)Create. Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem *myLoadBalancer* a přiřadí *myPublicIP* adresu konfigurace front-endové IP adresy.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Vytvoření sondy stavu na portu 80

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Vytvořte sondu stavu pomocí AZ Network disprobe Create a Monitorujte stav virtuálních počítačů. Sondu stavu protokolu TCP vytvoříte pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Následující příklad vytvoří sondu stavu *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení pro port 80
Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Pomocí příkazu *az network lb rule create* vytvořte pravidlo nástroje pro vyrovnávání zatížení [myLoadBalancerRuleWeb](/cli/azure/network/lb/rule#az-network-lb-rule-create) pro naslouchání na portu 80 ve front-endovém fondu *myFrontEndPool* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool* rovněž na portu 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s názvem *myVnet* s podsítí s názvem *mySubnet* v myResourceGroup pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* , která definuje příchozí připojení k virtuální síti pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Vytvořte pravidlo skupiny zabezpečení sítě s názvem *myNetworkSecurityGroupRule* pro port 80 pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
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
Vytvořte tři virtuální síťové karty pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create) a přidružte je k veřejné IP adrese a skupině zabezpečení sítě. Následující příklad vytvoří tři virtuální síťové karty. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Vytvoření serverů back-end
V tomto příkladu vytvoříte tři virtuální počítače umístěné v zóně 1 pro použití jako servery back-end pro nástroj pro vyrovnávání zatížení. Na virtuální počítače nainstalujete také NGINX a ověříte tak, že nástroj pro vyrovnávání zatížení byl úspěšně vytvořen.

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
Vytvořte virtuální počítače pomocí [AZ VM Create](/cli/azure/vm#az-vm-create). Následující příklad vytvoří tři virtuální počítače v zóně 1 a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
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

![Spuštěná aplikace Node.js](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Chcete-li vidět, že nástroj pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů v zóně 1, na kterých běží vaše aplikace, můžete vynutit aktualizaci webového prohlížeče.

## <a name="next-steps"></a>Další kroky
- Další informace o [Load Balanceru úrovně Standard](./load-balancer-standard-overview.md).



