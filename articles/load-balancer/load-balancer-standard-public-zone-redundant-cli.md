---
title: Zónově redundantní virtuálních počítačů pomocí Azure CLI můžete vyvažovat zatížení u | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit veřejné služby Load Balancer Standard s zóny redundantních front-endu pomocí Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: dbefe5324acb699abb0e06b8f3f464a91a6fa2e2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431126"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Nástroj pro vyrovnávání zatížení virtuálních počítačů ve všech zónách dostupnosti pomocí Azure CLI

Tento článek popisuje postup vytvořením veřejné [Load balancer úrovně Standard](https://aka.ms/azureloadbalancerstandard) s zónově redundantních front-endu zajistit redundanci zón bez závislosti na několik záznamů DNS. Jedna IP adresa front-endu je automaticky zónově redundantní.  Nástroj pro vyrovnávání zatížení, jednu IP adresu pomocí redundantních front-endu zóny můžete nyní přistupovat jakýkoli virtuální počítač ve virtuální síti v rámci oblasti, která je ve všech zónách dostupnosti. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Další informace o používání zón dostupnosti s Load Balancerem úrovně Standard najdete v tématu o [Load Balanceru úrovně Standard a zónách dostupnosti](load-balancer-standard-availability-zones.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento kurz vyžaduje použití Azure CLI verze 2.0.17 nebo vyšší.  Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSLB* v *westeurope* umístění:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Vytvoření zónově redundantní veřejné IP Standard
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Zónově redundantních front-endu obsluhují všechny zóny dostupnosti v oblasti současně. Vytvoření zóny redundantní veřejnou IP adresu s [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Když vytvoříte standardní veřejnou IP adresu, je zónově redundantní ve výchozím nastavení.

Následující příklad vytvoří zónu redundantní veřejnou IP adresu s názvem *myPublicIP* v *myResourceGroupLoadBalancer* skupinu prostředků.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>Vytvořit Azure Load Balancer úrovně Standard
Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
- Front-endový fond IP adres, který přijímá příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
- Back-endový fond IP adres, kam front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.
- Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
- Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Vytvořte nástroj pro vyrovnávání zatížení Standard s [az network lb vytvořit](/cli/azure/network/lb#az-network-lb-create). Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem *myLoadBalancer* a přiřadí *myPublicIP* ke konfiguraci front-end IP adresy.

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

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Vytvořte sondu stavu pomocí az network lb probe vytvořit pro monitorování stavu virtuálních počítačů. Sondu stavu protokolu TCP vytvoříte pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Následující příklad vytvoří sondu stavu *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Vytvořte pravidlo nástroje pro vyrovnávání zatížení pro port 80
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

Vytvoření virtuální sítě s názvem *myVnet* s podsítí *mySubnet* myResourceGroup pomocí [az network vnet vytvořit](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořit skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* definovat příchozí připojení k virtuální síti s [az network nsg vytvořit](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Vytvořte pravidlo skupiny zabezpečení sítě s názvem *myNetworkSecurityGroupRule* pro port 80 s [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

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
Vytvořte tři virtuální síťové karty s [az network nic vytvořit](/cli/azure/network/nic#az-network-nic-create) a přidružit je k veřejné IP adrese a skupině zabezpečení sítě. Následující příklad vytvoří šesti virtuálních síťových karet. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

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
V tomto příkladu vytvoříte tři virtuální počítače v zóně 1, zóna 2 a zóny 3 se použije jako servery back-endu nástroje pro vyrovnávání zatížení. Na virtuální počítače k ověření úspěšného vytvoření nástroje pro vyrovnávání zatížení taky nainstalujete server NGINX.

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

### <a name="create-the-zonal-virtual-machines"></a>Vytvoření zónového virtuálních počítačů
Vytvoření virtuálních počítačů s [az vm vytvořit](/cli/azure/vm#az-vm-create) v zóně 1, zóna 2 a zóna 3. Následující příklad vytvoří virtuální počítač v každé zóně a vygeneruje klíče SSH, pokud ještě neexistují:

Vytvoření virtuálního počítače v každé zóně (zóna 1, zóna 2 a zóna 3) *westeurope* umístění.

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

Získejte veřejnou IP adresu nástroje pro vyrovnávání zatížení pomocí [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Nezapomeňte, že příprava virtuálních počítačů několik minut trvá, a až pak do nich začne nástroj pro vyrovnávání zatížení distribuovat provoz. Zobrazí se aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštěná aplikace Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Chcete-li zobrazit nástroje pro vyrovnávání zatížení distribuovat provoz napříč virtuálními počítači ve všech třech zónách dostupnosti používající vaši aplikaci, můžete zastavení virtuálního počítače v konkrétní zóně a aktualizujte svůj prohlížeč.

## <a name="next-steps"></a>Další postup
- Další informace o [Load Balanceru úrovně Standard](./load-balancer-standard-overview.md)



