---
title: 'Rychlý start: Vytvoření Load balanceru úrovně Basic – Azure CLI'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit veřejný nástroj pro vyrovnávání zatížení pomocí rozhraní Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.custom: mvc
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: kumud
ms.openlocfilehash: 18c5b8f31b2a912eade360ebc2c5845782667250
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230216"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rychlý start: Vytvořte nástroj pro vyrovnávání zatížení vyrovnávat zatížení virtuálních počítačů pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak vytvořit Azure Load balanceru úrovně pro načtení vyrovnávat přenosy z Internetu mezi virtuálními počítači v Azure. K otestování nástroje pro vyrovnávání zatížení nasadíte dva virtuální počítače se serverem Ubuntu, které mezi sebou budou vyrovnávat zatížení webové aplikace.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group#create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroupLB* v umístění *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Pomocí příkazu [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) vytvořte veřejnou IP adresu *myPublicIP* ve skupině prostředků *myResourceGroupLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupLB --name myPublicIP
```

## <a name="create-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
  - Front-endový fond IP adres, který přijímá příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
  - Back-endový fond IP adres, kam front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.
  - Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
  - Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Pomocí příkazu [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) vytvořte veřejný Azure Load Balancer **myLoadBalancer**, který obsahuje front-endový fond **myFrontEndPool** a back-endový fond **myBackEndPool** přidružený k veřejné IP adrese **myPublicIP**, kterou jste vytvořili v předchozím kroku.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Pomocí příkazu [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Pomocí příkazu *myLoadBalancerRuleWeb* vytvořte pravidlo nástroje pro vyrovnávání zatížení [myLoadBalancerRuleWeb](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) pro naslouchání na portu 80 ve front-endovém fondu *myFrontEndPool* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool* rovněž na portu 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
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

Pomocí příkazu [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) vytvořte virtuální síť *myVnet* s podsítí *mySubnet* ve skupině prostředků *myResourceGroup*.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k vaší virtuální síti.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení přes port 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
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

Pomocí příkazu [az network nic create](/cli/azure/network/nic#az-network-nic-create) vytvořte tři síťová rozhraní a přiřaďte je k veřejné IP adrese a skupině zabezpečení sítě. 

```azurecli-interactive
for i in `seq 1 2`; do
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

V tomto příkladu vytvoříte tři virtuální počítače, které se použijí jako servery back-end pro nástroj pro vyrovnávání zatížení. K ověření úspěšného vytvoření nástroje pro vyrovnávání zatížení také na virtuální počítače nainstalujete server NGINX.

### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Vytvořte skupinu dostupnosti pomocí příkazu [az vm availabilityset create](/cli/azure/network/nic#az-network-availabilityset-create).

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupLB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

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
 
Vytvořte virtuální počítače pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
Nasazení virtuálních počítačů může několik minut trvat.

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

K získání veřejné IP adresy nástroje pro vyrovnávání zatížení použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
![Test nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-arm-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myResourceGroupLB
```


## <a name="next-steps"></a>Další postup
V rámci tohoto rychlého startu jste vytvořili Load Balancer úrovně Basic, připojili jste k němu virtuální počítače, nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Chcete-li zjistit další informace o službě Azure Load Balancer, přejděte ke kurzům pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
