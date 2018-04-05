---
title: Zónově redundantní virtuální počítače pomocí rozhraní příkazového řádku Azure můžete vyrovnávat zatížení | Microsoft Docs
description: Naučte se vytvořit standardní veřejné zatížení na vyrovnávání s zóny redundantní front-endu pomocí rozhraní příkazového řádku Azure
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
ms.openlocfilehash: 29dcfaad840b5498dd859082ce11655a4f1fe8af
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Nástroj pro vyrovnávání zatížení virtuálních počítačů mezi všechny zóny dostupnosti pomocí rozhraní příkazového řádku Azure

Tento článek popisuje postup procesem vytvoření veřejné [standardní nástroje pro vyrovnávání zatížení](https://aka.ms/azureloadbalancerstandard) s zónově redundantní front-endu k dosažení zálohování zóny bez závislosti na více záznamů DNS. Jediné front-end IP adresy je automaticky zónově redundantní.  Používat redundantní front-endu zóny pro nástroj pro vyrovnávání zatížení, s jedinou IP adresu můžete nyní dosáhnout žádné virtuální počítače ve virtuální síti v rámci oblasti, která je v rámci všech zón dostupnosti. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Další informace o použití zón dostupnosti se nástroj pro vyrovnávání zatížení najdete v tématu [nástroj pro vyrovnávání zatížení a dostupnosti zón](load-balancer-standard-availability-zones.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, tento kurz vyžaduje, že používáte Azure CLI verze 2.0.17 nebo vyšší.  Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Podpora pro dostupnost zóny je k dispozici pro vyberte prostředků Azure a oblasti a rodiny velikost virtuálního počítače. Další informace o tom, jak začít pracovat a které prostředky Azure, oblasti a rodiny velikost virtuálního počítače můžete zkusit dostupnost zóny s, najdete v části [přehled dostupnosti zón](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupSLB* v *westeurope* umístění:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Vytvořit zónu redundantní public IP Standard
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Zónově redundantní front-endu je poskytovaný dostupnosti zón v oblasti současně. Vytvoření zóny redundantní veřejné IP adresy s [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Když vytvoříte standardní veřejnou IP adresu, je zónově redundantní ve výchozím nastavení.

Následující příklad vytvoří zónu redundantní veřejnou IP adresu s názvem *myPublicIP* v *myResourceGroupLoadBalancer* skupinu prostředků.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>Vytvořit Standard pro vyrovnávání zatížení Azure
Tato část popisuje, o tom, jak můžete vytvořit a konfigurovat následující součásti nástroje pro vyrovnávání zatížení:
- fond IP front-endu, která přijímá příchozí síťový provoz na nástroje pro vyrovnávání zatížení.
- fond back-end IP kde fondu front-endu odešle zatížení skupinu s vyrovnáváním zatížení sítě.
- Test stavu, který určuje stav instancí virtuálních počítačů v back-end.
- pravidlo Vyrovnávání zatížení, která definuje, jak se provoz rozděluje k virtuálním počítačům.

### <a name="create-the-load-balancer"></a>Vytvořit nástroj pro vyrovnávání zatížení
Vytvořit nástroj pro vyrovnávání zatížení standardní s [az sítě lb vytvořit](/cli/azure/network/lb#az_network_lb_create). Následující příklad vytvoří nástroj pro vyrovnávání zatížení s názvem *myLoadBalancer* a přiřadí *myPublicIP* ke konfiguraci front-end IP adresy.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Vytvoření test stavu na portu 80

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Test stavu vytvoření se test vyrovnáváním zatížení sítě az vytvořit tak, aby monitorovala virtuálních počítačů. Sondu stavu protokolu TCP vytvoříte pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Následující příklad vytvoří sondu stavu *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Vytvořit pravidlo Vyrovnávání zatížení pro port 80
Pravidlo Vyrovnávání zatížení definuje front-endové konfiguraci protokolu IP pro příchozí provoz a fond back-end IP příjem provozu, společně s požadovaný zdrojový a cílový port. Vytvořit pravidlo služby load balancer *myLoadBalancerRuleWeb* s [vytvořit pravidlo vyrovnáváním zatížení sítě az](/cli/azure/network/lb/rule#az_network_lb_rule_create) pro naslouchání na portu 80 ve fondu front-endu *myFrontEndPool* a odesílání Vyrovnávání zatížení sítě síťový provoz do fondu adres back-end *myBackEndPool* také používá port 80.

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
Před nasazením některé virtuální počítače a nástroj pro vyrovnávání zatížení můžete otestovat, vytvořte doprovodné materiály virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořit virtuální síť s názvem *myVnet* s podsítí s názvem *mySubnet* v myResourceGroup pomocí [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořit skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* definovat příchozí připojení k virtuální síti s [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Vytvoření pravidla skupiny zabezpečení sítě s názvem *myNetworkSecurityGroupRule* pro port 80 s [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

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
Vytvořte tři virtuální síťové adaptéry se [síťových adaptérů sítě az vytvořit](/cli/azure/network/nic#az_network_nic_create) a je přidružte veřejnou IP adresu a skupinu zabezpečení sítě. Následující příklad vytvoří šesti virtuálních síťových karet. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

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
## <a name="create-backend-servers"></a>Vytvoření back-end serverů
V tomto příkladu můžete vytvořit tři virtuální počítače umístěné v zónu 1, 2 zóny a zónu 3 má být použit jako back-end serverů nástroje pro vyrovnávání zatížení. Můžete taky nainstalovat NGINX virtuálních počítačů k ověření, že nástroj pro vyrovnávání zatížení byl úspěšně vytvořen.

### <a name="create-cloud-init-config"></a>Vytvoření konfigurace cloud-init

Konfigurační soubor init cloudu můžete použít k instalaci NGINX a spuštění aplikace Node.js "Zdravím svět" na virtuální počítač s Linuxem. V aktuálním prostředí vytvořte soubor s názvem init.txt cloudu a zkopírujte a vložte následující konfigurace do prostředí. Je zkopírovat celý soubor cloudu init správně, obzvláště první řádek:

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

### <a name="create-the-zonal-virtual-machines"></a>Vytvoření oblastmi virtuálních počítačů
Vytvoření virtuálních počítačů s [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) v zóně 1, 2 zóně a zóny 3. Následující příklad vytvoří virtuální počítač v každé zóně a generuje klíče SSH, pokud už neexistují:

Vytvořit virtuální počítače v zóně 1

```azurecli-interactive
 az vm create \
--resource-group myResourceGroupSLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone $i \
--custom-data cloud-init.txt
```
## <a name="test-the-load-balancer"></a>Testovací nástroje pro vyrovnávání zatížení

Získat veřejnou IP adresu pomocí služby Vyrovnávání zatížení [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Nezapomeňte, že příprava virtuálních počítačů několik minut trvá, a až pak do nich začne nástroj pro vyrovnávání zatížení distribuovat provoz. Zobrazí se aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštěná aplikace Node.js](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Informace o vyrovnávání zatížení napříč virtuálními počítači ve všech tří dostupnosti zónách používající vaši aplikaci distribuci přenosů, můžete zastavení virtuálního počítače v konkrétní zóně a aktualizujte webový prohlížeč.

## <a name="next-steps"></a>Další postup
- Další informace o [nástroj pro vyrovnávání zatížení](./load-balancer-standard-overview.md)



