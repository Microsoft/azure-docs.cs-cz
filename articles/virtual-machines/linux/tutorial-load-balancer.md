---
title: Kurz – vyrovnávání zatížení virtuálních počítačů s Linuxem v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí Azure CLI vytvořit nástroj pro vyrovnávání zatížení pro vysoce dostupnou a zabezpečenou aplikaci na třech virtuálních počítačích s Linuxem.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 66c6a565fff81e1c0e39075502a6a7d3d8ffa7a6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162289"
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli"></a>Kurz: Vyrovnávání zatížení virtuálních počítačů s Linuxem v Azure za účelem vytvoření vysoce dostupné aplikace pomocí Azure CLI

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto kurzu se seznámíte s různými komponentami nástroje pro vyrovnávání zatížení Azure, které distribuují provoz a zajišťují vysokou dostupnost. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření nástroje pro vyrovnávání zatížení Azure
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
> * Vytvoření pravidel provozu pro nástroj pro vyrovnávání zatížení
> * Vytvoření základní aplikace Node.js pomocí cloud-init
> * Vytvoření virtuálních počítačů a jejich připojení k nástroji pro vyrovnávání zatížení
> * Zobrazení nástroje pro vyrovnávání zatížení v akci
> * Přidání virtuálních počítačů do nástroje pro vyrovnávání zatížení a jejich odebrání

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte pro tento kurz mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="azure-load-balancer-overview"></a>Azure Load Balancer – přehled
Nástroj pro vyrovnávání zatížení Azure je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP), který poskytuje vysokou dostupnost díky distribuci příchozího provozu mezi virtuální počítače v dobrém stavu. Sonda stavu nástroje pro vyrovnávání zatížení na všech virtuálních počítačích monitoruje daný port a distribuuje provoz pouze do virtuálních počítačů, které jsou v provozu.

Nadefinujete konfiguraci front-endových IP adres, která obsahuje jednu nebo více veřejných IP adres. Tato konfigurace front-endových IP adres povoluje přístup k vašemu nástroji pro vyrovnávání zatížení a vašim aplikacím přes internet. 

Virtuální počítače se k nástroji pro vyrovnávání zatížení připojují pomocí své virtuální síťové karty. Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení.

Pro řízení toku provozu definujete pravidla nástroje pro vyrovnávání zatížení pro konkrétní porty a protokoly, které se mapují na vaše virtuální počítače.

Pokud jste postupovali podle předchozího kurzu věnovaného [vytvoření škálovací sady virtuálních počítačů](tutorial-create-vmss.md), nástroj pro vyrovnávání zatížení už se pro vás vytvořil. Všechny tyto komponenty se pro vás nakonfigurovaly jako součást škálovací sady.


## <a name="create-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure
Tato část podrobně popisuje vytvoření a konfiguraci jednotlivých komponent nástroje pro vyrovnávání zatížení. Než budete vytvoříte nástroj pro vyrovnávání zatížení, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroupLoadBalancer* v umístění *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* ve skupině prostředků *myResourceGroupLoadBalancer*:

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Vytvořte nástroj pro vyrovnávání zatížení pomocí příkazu [az network lb create](/cli/azure/network/lb#az_network_lb_create). Následující příklad vytvoří nástroj pro vyrovnávání zatížení *myLoadBalancer* a přiřadí adresu *myPublicIP* ke konfiguraci front-end IP adres:

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Ve výchozím nastavení se virtuální počítač odebere z distribuce nástroje pro vyrovnávání zatížení po dvou selháních po sobě v 15sekundových intervalech. Sondu stavu můžete vytvořit na základě protokolu nebo konkrétní stránky kontroly stavu pro vaši aplikaci. 

Následující příklad vytvoří sondu protokolu TCP. Pokud potřebujete jemněji odstupňované kontroly stavu, můžete vytvářet i vlastní sondy protokolu HTTP. Pokud použijete vlastní sondu protokolu HTTP, musíte vytvořit stránku kontroly stavu, například *healthcheck.js*. Aby nástroj pro vyrovnávání zatížení udržel hostitele v oběhu, musí sonda vracet odpověď **HTTP 200 OK**.

Sondu stavu protokolu TCP vytvoříte pomocí příkazu [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Následující příklad vytvoří sondu stavu *myHealthProbe*:

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Abyste zajistili, že provoz budou přijímat pouze virtuální počítače, které jsou v pořádku, nadefinujete také sondu stavu, která se má použít.

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Následující příklad vytvoří pravidlo *myLoadBalancerRule*, použije sondu stavu *myHealthProbe* a nastaví vyrovnávání provozu na portu *80*:

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě. Další informace o virtuálních sítích najdete v kurzu [Správa virtuálních sítí Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Vytvoření síťových prostředků
Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť *myVnet* s podsítí *mySubnet*:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Skupinu zabezpečení sítě přidáte pomocí příkazu [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Vytvořte pravidlo skupiny zabezpečení sítě pomocí příkazu [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Následující příklad vytvoří pravidlo skupiny zabezpečení sítě *myNetworkSecurityGroupRule*:

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Virtuální síťové karty se vytvářejí pomocí příkazu [az network nic create](/cli/azure/network/nic). Následující příklad vytvoří tři virtuální síťové karty. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Po vytvoření všech tří virtuálních síťových karet pokračujte k dalšímu kroku.


## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

### <a name="create-cloud-init-config"></a>Vytvoření konfigurace cloud-init
V předchozím kurzu týkajícím se [postupu přizpůsobení virtuálního počítače s Linuxem při prvním spuštění](tutorial-automate-vm-deployment.md), jste se dozvěděli, jak automatizovat přizpůsobení virtuálního počítače s prostředím cloud-init. Stejný konfigurační soubor cloud-init můžete použít k instalaci serveru NGINX a spuštění jednoduché aplikace Hello World v Node.js v dalším kroku. Pokud chcete vidět nástroj pro vyrovnávání zatížení v akci, na konci tohoto kurzu otevřete tuto jednoduchou aplikaci ve webovém prohlížeči.

V aktuálním prostředí vytvořte soubor *cloud-init.txt* a vložte do něj následující konfiguraci. Soubor vytvořte například v Cloud Shellu, pokud nepracujete na místním počítači. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

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

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Pokud chcete zlepšit vysokou dostupnost aplikace, umístěte své virtuální počítače do skupiny dostupnosti. Další informace o skupinách dostupnosti najdete v předchozím kurzu [Vytváření vysoce dostupných virtuálních počítačů](tutorial-availability-sets.md).

Vytvořte skupinu dostupnosti pomocí příkazu [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). Následující příklad vytvoří skupinu dostupnosti *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Teď můžete vytvořit virtuální počítače pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří tři virtuální počítače a vygeneruje klíče SSH, pokud ještě neexistují:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Jakmile vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. Parametr `--no-wait` znamená, že se nečeká na dokončení všech úloh. Může trvat dalších několik minut, než k aplikaci budete mít přístup. Sonda stavu nástroje pro vyrovnávání zatížení automaticky rozpozná, jakmile bude aplikace spuštěná na všech virtuálních počítačích. Jakmile bude aplikace spuštěná, pravidlo nástroje pro vyrovnávání zatížení začne distribuovat provoz.


## <a name="test-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
Získejte veřejnou IP adresu svého nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Následující příklad získá dříve vytvořenou IP adresu *myPublicIP*:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Nezapomeňte, že příprava virtuálních počítačů několik minut trvá, a až pak do nich začne nástroj pro vyrovnávání zatížení distribuovat provoz. Zobrazí se aplikace, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Spuštěná aplikace Node.js](./media/tutorial-load-balancer/running-nodejs-app.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny tři virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče.


## <a name="add-and-remove-vms"></a>Přidání a odebrání virtuálních počítačů
Na virtuálních počítačích, na kterých je vaše aplikace spuštěná, možná budete potřebovat provést údržbu, například nainstalovat aktualizace operačního systému. Abyste si poradili se zvýšením provozu do vaší aplikace, možná budete muset přidat další virtuální počítače. V této části se dozvíte, jak z nástroje pro vyrovnávání zatížení odebrat virtuální počítač nebo ho do něj přidat.

### <a name="remove-a-vm-from-the-load-balancer"></a>Odebrání virtuálního počítače z nástroje pro vyrovnávání zatížení
Virtuální počítač můžete odebrat z fondu back-end adres pomocí příkazu [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_remove). Následující příklad odebere virtuální síťovou kartu virtuálního počítače **myVM2** z nástroje pro vyrovnávání zatížení *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi zbývající dva virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče. Teď můžete na virtuálním počítači provést údržbu, například nainstalovat aktualizace operačního systému nebo provést restartování virtuálního počítače.

Pokud chcete zobrazit seznam virtuálních počítačů s virtuálními síťovými kartami připojenými k nástroji pro vyrovnávání zatížení, použijte příkaz [az network lb address-pool show](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_show). Následujícím způsobem zadejte dotaz s filtrem podle ID virtuální síťové karty:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

Výstup bude podobný jako v následujícím příkladu, který ukazuje, že virtuální síťová karta virtuálního počítače 2 už není součástí fondu back-end adres:

```bash
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Přidání virtuálního počítače do nástroje pro vyrovnávání zatížení
Po provedení údržby virtuálního počítače, nebo pokud potřebujete rozšířit kapacitu, můžete přidat virtuální počítač do fondu back-end adres pomocí příkazu [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Následující příklad přidá virtuální síťovou kartu virtuálního počítače **myVM2** do nástroje pro vyrovnávání zatížení *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Pokud chcete ověřit připojení virtuální síťové karty k fondu back-end adres, znovu použijte příkaz [az network lb address-pool show](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_show) z předchozího kroku.


## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili nástroj pro vyrovnávání zatížení a připojili jste k němu virtuální počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření nástroje pro vyrovnávání zatížení Azure
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
> * Vytvoření pravidel provozu pro nástroj pro vyrovnávání zatížení
> * Vytvoření základní aplikace Node.js pomocí cloud-init
> * Vytvoření virtuálních počítačů a jejich připojení k nástroji pro vyrovnávání zatížení
> * Zobrazení nástroje pro vyrovnávání zatížení v akci
> * Přidání virtuálních počítačů do nástroje pro vyrovnávání zatížení nebo jejich odebrání

Přejděte k dalšímu kurzu, kde najdete další informace o komponentách virtuální sítě Azure.

> [!div class="nextstepaction"]
> [Správa virtuálních počítačů a virtuálních sítí](tutorial-virtual-network.md)
