---
title: 'Rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení – Azure CLI'
titleSuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit veřejný nástroj pro vyrovnávání zatížení pomocí rozhraní Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2b22c00845b38d2edea2d78497fb4b46a51896d4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587124"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rychlý start: Vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure CLI

Začínáme s Azure Load Balancer pomocí rozhraní příkazového řádku Azure k vytvoření veřejného nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create):

* Název **CreatePubLBQS-RG**. 
* V umístění **eastus** .

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Prostředky služby Load Balancer úrovně Standard vytvořené pro rychlý Start." border="false":::

## <a name="configure-virtual-network---standard"></a>Konfigurace virtuální sítě – standardní

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-createt):

* S názvem **myVNet**.
* Předpona adresy **10.1.0.0/16**
* Podsíť s názvem **myBackendSubnet**.
* Předpona podsítě **10.1.0.0/24**.
* Ve skupině prostředků **CreatePubLBQS-RG** .
* Umístění **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

K vytvoření veřejné IP adresy pro hostitele bastionu použijte [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) :

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myBastionIP**.
* V **CCreatePubLBQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Vytvoření podsítě bastionu

Pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) vytvořte podsíť bastionu:

* S názvem **AzureBastionSubnet**.
* Předpona adresy **10.1.1.0/24**.
* Ve virtuální síti **myVNet**.
* Ve skupině prostředků **CreatePubLBQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Vytvořit hostitele bastionu

Pomocí [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) Vytvořte hostitele bastionu:

* S názvem **myBastionHost**.
* V **CreatePubLBQS-RG**.
* Přidruženo k veřejné IP **myBastionIP**.
* Přidruženo k virtuální síti **myVNet**.
* V umístění **eastus** .

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Nasazení hostitele Azure bastionu může trvat několik minut.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

U standardního nástroje pro vyrovnávání zatížení musí být virtuální počítače na back-endové adrese k dispozici pro síťová rozhraní, která patří do skupiny zabezpečení sítě. 

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create):

* S názvem **myNSG**.
* Ve skupině prostředků **CreatePubLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

* S názvem **myNSGRuleHTTP**.
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Protokol **(*)**.
* Směr **příchozí**.
* Zdroj **(*)**.
* Cíl **(*)**.
* Cílový port portu **80**.
* Přístup **Povolte**.
* Priorita **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
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

## <a name="create-backend-servers---standard"></a>Vytvoření back-end serverů – Standard

V této části vytvoříte:

* Tři síťová rozhraní pro virtuální počítače.
* Tři virtuální počítače, které se mají použít jako servery back-end pro nástroj pro vyrovnávání zatížení.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Vytvořte tři síťová rozhraní pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create):

* Pojmenované **myNicVM1**, **myNicVM2** a **myNicVM3**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Ve skupině zabezpečení sítě **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítače pomocí [AZ VM Create](/cli/azure/vm#az-vm-create):

### <a name="vm1"></a>VM1
* S názvem **myVM1**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* **Win2019datacenter** image virtuálního počítače.
* V **zóna 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* S názvem **myVM2**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* **Win2019datacenter** image virtuálního počítače.
* V **zóna 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* S názvem **myVM3**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM3**.
* **Win2019datacenter** image virtuálního počítače.
* V **zóna 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
Nasazení virtuálních počítačů může trvat několik minut.

## <a name="create-a-public-ip-address---standard"></a>Vytvoření veřejné IP adresy – Standard

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. 

Použijte [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) to:

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myPublicIP**.
* V **CreatePubLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Vytvoření redundantní veřejné IP adresy v Zóna 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Vytvořit službu Load Balancer úrovně Standard

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [AZ Network](/cli/azure/network/lb#az-network-lb-create)diskont Create:

* S názvem **myLoadBalancer**.
* Front-endového fondu s názvem **myFrontEnd**.
* Back-end fond s názvem **myBackEndPool**.
* Přidruženo k **myPublicIP** veřejné IP adresy, kterou jste vytvořili v předchozím kroku. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network diskont Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe** sondy stavu.
* Protokol **TCP**.
* Časový limit nečinnosti **15 minut**.
* Povolte resetování protokolu TCP.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
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
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Přidání virtuálních počítačů do back-endového fondu služby Load Balancer

Přidejte virtuální počítače do fondu back-end pomocí [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* Ve fondu back-end adres **myBackEndPool**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Vytvořit konfiguraci odchozího pravidla
Odchozí pravidla nástroje pro vyrovnávání zatížení konfigurují odchozí SNAT pro virtuální počítače ve fondu back-endu. 

Další informace o odchozích připojeních najdete v tématu [odchozí připojení v Azure](load-balancer-outbound-connections.md).

Veřejnou IP adresu nebo předponu lze použít pro odchozí konfiguraci.

### <a name="public-ip"></a>Veřejná IP adresa

Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) vytvořte jednu IP adresu pro odchozí připojení.  

* S názvem **myPublicIPOutbound**.
* V **CreatePubLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Vytvoření redundantní veřejné IP adresy v Zóna 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Předpona veřejné IP adresy

Pomocí [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) vytvořte předponu veřejné IP adresy pro odchozí připojení.

* S názvem **myPublicIPPrefixOutbound**.
* V **CreatePubLBQS-RG**.
* Délka předpony **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Postup při vytváření redundantní předpony veřejné IP adresy pro oblast v Zóna 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Další informace o škálování odchozího NAT a odchozího připojení najdete v tématu [škálování odchozího překladu adres (NAT) s několika IP adresami](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Vytvoření odchozí konfigurace IP adresy front-endu

Vytvořte novou konfiguraci IP adresy front-endu pomocí [AZ Network disendu-IP Create ](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create):

Vyberte příkazy veřejné IP adresy nebo předpony veřejných IP adres na základě rozhodnutí v předchozím kroku.

#### <a name="public-ip"></a>Veřejná IP adresa

* S názvem **myFrontEndOutbound**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Přidruženo k veřejné IP adrese **myPublicIPOutbound**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Předpona veřejné IP adresy

* S názvem **myFrontEndOutbound**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Přidruženo k **myPublicIPPrefixOutbound** předpony veřejných IP adres.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Vytvořit odchozí fond

Vytvořte nový odchozí fond pomocí [AZ Network Address-Pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create):

* S názvem **myBackEndPoolOutbound**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Vytvořit odchozí pravidlo

Vytvořte nové odchozí pravidlo pro odchozí back-end fond pomocí [AZ Network Outbound-rule Create](/cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-create):

* S názvem **myOutboundRule**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení
* Přidruženo k front-endu **myFrontEndOutbound**.
* Protokol **vše**.
* Časový limit nečinnosti **15**.
* porty **10000** odchozí.
* Přidruženo k **myBackEndPoolOutbound** fondu back-endu.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Přidat virtuální počítače do odchozího fondu

Přidejte virtuální počítače do odchozího fondu pomocí [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):


* Ve fondu back-end adres **myBackEndPoolOutbound**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Základní prostředky nástroje pro vyrovnávání zatížení vytvořené v rychlém startu." border="false"::: 4m

## <a name="configure-virtual-network---basic"></a>Konfigurace virtuální sítě – Basic

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create):

* S názvem **myVNet**.
* Předpona adresy **10.1.0.0/16**
* Podsíť s názvem **myBackendSubnet**.
* Předpona podsítě **10.1.0.0/24**.
* Ve skupině prostředků **CreatePubLBQS-RG** .
* Umístění **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

K vytvoření veřejné IP adresy pro hostitele bastionu použijte [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) :

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myBastionIP**.
* V **CreatePubLBQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Vytvoření podsítě bastionu

Pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) vytvořte podsíť bastionu:

* S názvem **AzureBastionSubnet**.
* Předpona adresy **10.1.1.0/24**.
* Ve virtuální síti **myVNet**.
* Ve skupině prostředků **CreatePubLBQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Vytvořit hostitele bastionu

Pomocí [AZ Network bastionu Create](/cli/azure/network/bastion#az-network-bastion-create) Vytvořte hostitele bastionu:

* S názvem **myBastionHost**.
* V **CreatePubLBQS-RG**.
* Přidruženo k veřejné IP **myBastionIP**.
* Přidruženo k virtuální síti **myVNet**.
* V umístění **eastus** .

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Nasazení hostitele Azure bastionu může trvat několik minut.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

U standardního nástroje pro vyrovnávání zatížení musí být virtuální počítače na back-endové adrese k dispozici pro síťová rozhraní, která patří do skupiny zabezpečení sítě. 

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create):

* S názvem **myNSG**.
* Ve skupině prostředků **CreatePubLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

* S názvem **myNSGRuleHTTP**.
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Protokol **(*)**.
* Směr **příchozí**.
* Zdroj **(*)**.
* Cíl **(*)**.
* Cílový port portu **80**.
* Přístup **Povolte**.
* Priorita **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
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

## <a name="create-backend-servers---basic"></a>Vytvoření back-end serverů – Basic

V této části vytvoříte:

* Tři síťová rozhraní pro virtuální počítače.
* Skupina dostupnosti pro virtuální počítače
* Tři virtuální počítače, které se mají použít jako servery back-end pro nástroj pro vyrovnávání zatížení.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Vytvořte tři síťová rozhraní pomocí [AZ Network nic Create](/cli/azure/network/nic#az-network-nic-create):


* Pojmenované **myNicVM1**, **myNicVM2** a **myNicVM3**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet** podsíti.
* Ve skupině zabezpečení sítě **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Vytvořit skupinu dostupnosti pro virtuální počítače

Vytvořte skupinu dostupnosti pomocí [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create):

* S názvem **myAvSet**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Umístění **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítače pomocí [AZ VM Create](/cli/azure/vm#az-vm-create):

### <a name="vm1"></a>VM1
* S názvem **myVM1**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* **Win2019datacenter** image virtuálního počítače.
* V **zóna 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* S názvem **myVM2**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* **Win2019datacenter** image virtuálního počítače.
* V **zóna 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* S názvem **myVM3**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Připojeno k síťovému rozhraní **myNicVM3**.
* **Win2019datacenter** image virtuálního počítače.
* V **zóna 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
Nasazení virtuálních počítačů může trvat několik minut.

## <a name="create-a-public-ip-address---basic"></a>Vytvoření veřejné IP adresy – základní

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. 

Použijte [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) to:

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myPublicIP**.
* V **CreatePubLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Basic

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [AZ Network](/cli/azure/network/lb#az-network-lb-create)diskont Create:

* S názvem **myLoadBalancer**.
* Front-endového fondu s názvem **myFrontEnd**.
* Back-end fond s názvem **myBackEndPool**.
* Přidruženo k **myPublicIP** veřejné IP adresy, kterou jste vytvořili v předchozím kroku. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
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

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network diskont Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe** sondy stavu.
* Protokol **TCP**.
* Časový limit nečinnosti **15 minut**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
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

Přidejte virtuální počítače do fondu back-end pomocí [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* Ve fondu back-end adres **myBackEndPool**.
* Ve skupině prostředků **CreatePubLBQS-RG**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>Instalace služby IIS

K instalaci služby IIS na virtuálních počítačích použijte [AZ VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) a nastavte výchozí web na název počítače.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

K získání veřejné IP adresy nástroje pro vyrovnávání zatížení použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testování Load Balanceru" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu

* Vytvořili jste standardní nebo veřejný Nástroj pro vyrovnávání zatížení.
* Připojené virtuální počítače. 
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu.
* Otestování nástroje pro vyrovnávání zatížení.

Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte tady:
> [!div class="nextstepaction"]
> [Co je Azure Load Balancer?](load-balancer-overview.md)