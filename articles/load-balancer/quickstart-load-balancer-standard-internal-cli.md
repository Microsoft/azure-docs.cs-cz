---
title: 'Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení – Azure CLI'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 852589e7ba1896ad493c5b8c300240490a516b64
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518239"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure CLI

Začínáme s Azure Load Balancer pomocí rozhraní příkazového řádku Azure k vytvoření veřejného nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create):

* Název **CreateIntLBQS-RG**. 
* V umístění **eastus** .

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítače a nasadíte Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* S názvem **myVNet**.
* Předpona adresy **10.1.0.0/16**
* Podsíť s názvem **myBackendSubnet**.
* Předpona podsítě **10.1.0.0/24**.
* Ve skupině prostředků **CreateIntLBQS-RG** .
* Umístění **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

U standardního nástroje pro vyrovnávání zatížení musí být virtuální počítače na back-endové adrese k dispozici pro síťová rozhraní, která patří do skupiny zabezpečení sítě. 

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* S názvem **myNSG**.
* Ve skupině prostředků **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* S názvem **myNSGRuleHTTP**.
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Protokol **(*)**.
* Směr **příchozí**.
* Zdroj **(*)**.
* Cíl **(*)**.
* Cílový port portu **80**.
* Přístup **Povolte**.
* Priorita **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Vytvořte dvě síťová rozhraní pomocí [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* S názvem **myNicVM1**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Ve skupině zabezpečení sítě **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* S názvem **myNicVM2**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Ve skupině zabezpečení sítě **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte:

* Konfigurační soubor cloudu s názvem **cloud-init.txt** pro konfiguraci serveru.
* Dva virtuální počítače, které se mají použít jako servery back-end pro nástroj pro vyrovnávání zatížení.

### <a name="create-cloud-init-configuration-file"></a>Vytvoření konfiguračního souboru Cloud-init

K instalaci NGINX a spuštění aplikace Node.js Hello World na virtuálním počítači se systémem Linux použijte konfigurační soubor Cloud-init. 

V aktuální prostředí vytvořte soubor s názvem cloud-init.txt. Zkopírujte a vložte do prostředí následující konfiguraci. Ujistěte se, že jste správně zkopírovali celý soubor Cloud-init, zejména první řádek:

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

Vytvořte virtuální počítače pomocí [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* S názvem **myVM1**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* **UbuntuLTS** image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* S názvem **myVM2**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* **UbuntuLTS** image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

Nasazení virtuálních počítačů může trvat několik minut.

## <a name="create-standard-load-balancer"></a>Vytvořit službu Load Balancer úrovně Standard

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [AZ Network](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)diskont Create:

* S názvem **myLoadBalancer**.
* Front-endového fondu s názvem **myFrontEnd**.
* Back-end fond s názvem **myBackEndPool**.
* Přidruženo k virtuální síti **myVNet**.
* Přidruženo k **myBackendSubnet** podsíti back-endu.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network diskont Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe** sondy stavu.
* Protokol **TCP**.
* Časový limit nečinnosti **15 minut**.
* Povolte resetování protokolu TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>Virtuální počítače ve fondu back-end nebudou mít odchozí připojení k Internetu s touto konfigurací. </br> Další informace o poskytování odchozího připojení najdete v tématech: </br> **[Odchozí připojení v Azure](load-balancer-outbound-connections.md)**</br> Možnosti pro poskytování připojení: </br> **[Konfigurace nástroje pro vyrovnávání zatížení – pouze odchozí](egress-only.md)** </br> **[Co je Virtual Network NAT?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Přidání virtuálních počítačů do back-endového fondu služby Load Balancer

Přidejte virtuální počítače do fondu back-end pomocí [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* Ve fondu back-end adres **myBackEndPool**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Přidruženo k síťovému rozhraní **myNicVM1** a **ipconfig1**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Ve fondu back-end adres **myBackEndPool**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Přidruženo k síťovému rozhraní **myNicVM2** a **ipconfig1**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítače a nasadíte Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* S názvem **myVNet**.
* Předpona adresy **10.1.0.0/16**
* Podsíť s názvem **myBackendSubnet**.
* Předpona podsítě **10.1.0.0/24**.
* Ve skupině prostředků **CreateIntLBQS-RG** .
* Umístění **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

U standardního nástroje pro vyrovnávání zatížení musí být virtuální počítače na back-endové adrese k dispozici pro síťová rozhraní, která patří do skupiny zabezpečení sítě. 

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* S názvem **myNSG**.
* Ve skupině prostředků **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* S názvem **myNSGRuleHTTP**.
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Protokol **(*)**.
* Směr **příchozí**.
* Zdroj **(*)**.
* Cíl **(*)**.
* Cílový port portu **80**.
* Přístup **Povolte**.
* Priorita **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Vytvořte dvě síťová rozhraní pomocí [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* S názvem **myNicVM1**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Ve skupině zabezpečení sítě **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* S názvem **myNicVM2**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte:

* Konfigurační soubor cloudu s názvem **cloud-init.txt** pro konfiguraci serveru. 
* Skupina dostupnosti pro virtuální počítače
* Dva virtuální počítače, které se mají použít jako servery back-end pro nástroj pro vyrovnávání zatížení.

Chcete-li ověřit, zda byl nástroj pro vyrovnávání zatížení úspěšně vytvořen, nainstalujte NGINX na virtuální počítače.

### <a name="create-cloud-init-configuration-file"></a>Vytvoření konfiguračního souboru Cloud-init

K instalaci NGINX a spuštění aplikace Node.js Hello World na virtuálním počítači se systémem Linux použijte konfigurační soubor Cloud-init. 

V aktuální prostředí vytvořte soubor s názvem cloud-init.txt. Zkopírujte a vložte do prostředí následující konfiguraci. Ujistěte se, že jste správně zkopírovali celý soubor Cloud-init, zejména první řádek:

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

### <a name="create-availability-set-for-virtual-machines"></a>Vytvořit skupinu dostupnosti pro virtuální počítače

Vytvořte skupinu dostupnosti pomocí [AZ VM Availability-set Create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* S názvem **myAvSet**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Umístění **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítače pomocí [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* S názvem **myVM1**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* **UbuntuLTS** image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **myAvSet** skupině dostupnosti.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* S názvem **myVM2**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* **UbuntuLTS** image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

Nasazení virtuálních počítačů může trvat několik minut.

## <a name="create-basic-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Basic

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [AZ Network](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)diskont Create:

* S názvem **myLoadBalancer**.
* Front-endového fondu s názvem **myFrontEnd**.
* Back-end fond s názvem **myBackEndPool**.
* Přidruženo k virtuální síti **myVNet**.
* Přidruženo k **myBackendSubnet** podsíti back-endu.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network diskont Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe** sondy stavu.
* Protokol **TCP**.
* Časový limit nečinnosti **15 minut**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Přidání virtuálních počítačů do back-endového fondu služby Load Balancer

Přidejte virtuální počítače do fondu back-end pomocí [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* Ve fondu back-end adres **myBackEndPool**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Přidruženo k síťovému rozhraní **myNicVM1** a **ipconfig1**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Ve fondu back-end adres **myBackEndPool**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Přidruženo k síťovému rozhraní **myNicVM2** a **ipconfig1**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

### <a name="create-azure-bastion-public-ip"></a>Vytvoření veřejné IP adresy Azure bastionu

K vytvoření veřejné IP adresy pro hostitele bastionu použijte [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) :

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myBastionIP**.
* V **CreateIntLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Vytvořit podsíť Azure bastionu

Pomocí [AZ Network VNet Subnet Create](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) vytvořte podsíť:

* S názvem **AzureBastionSubnet**.
* Předpona adresy **10.1.1.0/24**.
* Ve virtuální síti **myVNet**.
* Ve skupině prostředků **CreateIntLBQS-RG**.

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Vytvořit hostitele Azure bastionu
Pomocí [AZ Network bastionu Create](https://docs.microsoft.com/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) Vytvořte hostitele bastionu:

* Pojmenovaný **myBastionHost**
* V **CreateIntLBQS-RG**
* Přidruženo k veřejné IP **myBastionIP**.
* Přidruženo k virtuální síti **myVNet**.
* V umístění **eastus** .

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
Nasazení hostitele bastionu bude trvat několik minut.

### <a name="create-test-virtual-machine"></a>Vytvořit testovací virtuální počítač

Vytvořte síťové rozhraní pomocí [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* S názvem **myNicTestVM**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Ve skupině zabezpečení sítě **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Vytvořte virtuální počítač pomocí [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* S názvem **myTestVM**.
* Ve skupině prostředků **CreateIntLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicTestVM**.
* **Win2019Datacenter** image virtuálního počítače.
* Vyberte hodnoty pro **\<adminpass>** a **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Může trvat několik minut, než se virtuální počítač nasadí.

### <a name="test"></a>Test

1. [Přihlaste se](https://portal.azure.com) na web Azure Portal.

1. Na obrazovce **Přehled** vyhledejte privátní IP adresu pro nástroj pro vyrovnávání zatížení. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky** a pak vyberte **myLoadBalancer**.

2. V **přehledu** **myLoadBalancer** si poznamenejte nebo zkopírujte adresu u pole **privátní IP adresa** .

3. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky** a potom ze seznamu prostředky vyberte **myTestVM** , která je umístěná ve skupině prostředků **CreateIntLBQS-RG** .

4. Na stránce **Přehled** vyberte **připojit** a pak **bastionu**.

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. Otevřete **Internet Explorer** v **myTestVM**.

8. Zadejte IP adresu z předchozího kroku do panelu Adresa v prohlížeči. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Vytvoření standardního interního nástroje pro vyrovnávání zatížení" border="true":::
   
Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) .

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu

* Vytvořili jste standardní nebo veřejný Nástroj pro vyrovnávání zatížení.
* Připojené virtuální počítače. 
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu.
* Otestování nástroje pro vyrovnávání zatížení.

Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte 
> [!div class="nextstepaction"]
> [Co je Azure Load Balancer?](load-balancer-overview.md)