---
title: 'Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení – Azure CLI'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788830"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Rychlý Start: vytvoření interního nástroje pro vyrovnávání zatížení pomocí rozhraní příkazového řádku Azure

Začínáme s Azure Load Balancer pomocí rozhraní příkazového řádku Azure k vytvoření interního nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Pojmenujte skupinu prostředků **CreateIntLBQS-RG** a určete umístění jako **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**Standardní SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. Při vytváření interního nástroje pro vyrovnávání zatížení je virtuální síť nakonfigurovaná jako síť pro nástroj pro vyrovnávání zatížení. Následující diagram znázorňuje prostředky vytvořené v rámci tohoto rychlého startu:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Prostředky služby Load Balancer úrovně Standard vytvořené pro rychlý Start." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítače a nasadíte Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

#### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_create). Určete následující nastavení:

* Pojmenovaný **myVNet**
* Předpona adresy **10.1.0.0/16**
* Podsíť s názvem **myBackendSubnet**
* Předpona podsítě pro **10.1.0.0/24**
* Ve skupině prostředků **CreateIntLBQS-RG**
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

#### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create) vytvoříte veřejnou IP adresu pro hostitele Azure bastionu. Určete následující nastavení:

* Vytvořte standardní zónu – redundantní veřejnou IP adresu s názvem **myBastionIP**
* V **CreateIntLBQS-RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Vytvoření podsítě Azure bastionu

Pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) vytvořte podsíť. Určete následující nastavení:

* Pojmenovaný **AzureBastionSubnet**
* Předpona adresy **10.1.1.0/24**
* Ve virtuální síti **myVNet**
* Ve skupině prostředků **CreateIntLBQS-RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Vytvoření hostitele služby Azure Bastion

K vytvoření hostitele použijte [AZ Network bastionu Create](/cli/azure/network/bastion#az_network_bastion_create) . Určete následující nastavení:

* Pojmenovaný **myBastionHost**
* V **CreateIntLBQS-RG**
* Přidruženo k veřejné IP **myBastionIP**
* Přidruženo k virtuální síti **myVNet**
* V umístění **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Nasazení hostitele Azure bastionu může trvat několik minut.

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

V případě standardního nástroje pro vyrovnávání zatížení zajistěte, aby vaše virtuální počítače měly síťová rozhraní, která patří do skupiny zabezpečení sítě. Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create). Určete následující nastavení:

* Pojmenovaný **myNSG**
* Ve skupině prostředků **CreateIntLBQS-RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Určete následující nastavení:

* Pojmenovaný **myNSGRuleHTTP**
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Protokol **(*)**
* Směr **příchozí**
* Zdroj **(*)**
* Cíl **(*)**
* Port cílového portu **80**
* Přístup **povolen**
* Priorita **200**

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

### <a name="create-back-end-servers"></a>Vytvoření back-end serverů

V této části vytvoříte:

* Tři síťová rozhraní pro virtuální počítače.
* Tři virtuální počítače, které se mají použít jako servery pro nástroj pro vyrovnávání zatížení.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Pomocí [AZ Network nic Create](/cli/azure/network/nic#az_network_nic_create)vytvořte tři síťová rozhraní. Určete následující nastavení:

* Pojmenované **myNicVM1**, **myNicVM2** a **myNicVM3**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Ve virtuální síti **myVNet**
* V podsíti **myBackendSubnet**
* V **myNSG** skupiny zabezpečení sítě

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítače pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Určete následující nastavení:

* Pojmenované **myVM1**, **myVM2** a **myVM3**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Připojeno k síťovému rozhraní **myNicVM1**, **myNicVM2** a **myNicVM3**
* **Win2019datacenter** image virtuálního počítače
* V **zóna 1**, **zóna 2** a **zóna 3**

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

Nasazení virtuálních počítačů může trvat několik minut.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

* Fond IP adres, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
* Druhý fond IP adres, ve kterém první fond odesílá síťový provoz s vyrovnáváním zatížení.
* Sonda stavu, která určuje stav instancí virtuálních počítačů.
* Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

#### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvořte veřejný Nástroj pro vyrovnávání zatížení pomocí [AZ Network](/cli/azure/network/lb#az_network_lb_create)diskont Create. Určete následující nastavení:

* Pojmenovaný **myLoadBalancer**
* Fond s názvem **myFrontEnd**
* Fond s názvem **myBackEndPool**
* Přidruženo k virtuální síti **myVNet**
* Přidruženo k podsíti **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Určete následující nastavení:

* Monitoruje stav virtuálních počítačů.
* Pojmenovaný **myHealthProbe**
* Protokol **TCP**
* **Port monitorování 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace protokolu IP pro příchozí provoz.
* Fond IP adres pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Určete následující nastavení:

* Pojmenovaný **myHTTPRule**
* Naslouchání na **portu 80** ve fondu **myFrontEnd**
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu adres **myBackEndPool** pomocí **portu 80** 
* Použití sondy stavu **myHealthProbe**
* Protokol **TCP**
* Časový limit nečinnosti **15 minut**
* Povolit resetování protokolu TCP

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
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>Přidání virtuálních počítačů do fondu nástroje pro vyrovnávání zatížení

Přidejte virtuální počítače do fondu back-end pomocí [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Určete následující nastavení:

* Ve fondu adres **myBackEndPool**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Přidruženo k síťovému rozhraní **myNicVM1**, **myNicVM2** a **myNicVM3**
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.

V této části vytvoříte Nástroj pro vyrovnávání zatížení, který vyrovnává zatížení virtuálních počítačů. Při vytváření interního nástroje pro vyrovnávání zatížení je virtuální síť nakonfigurovaná jako síť pro nástroj pro vyrovnávání zatížení. Následující diagram znázorňuje prostředky vytvořené v rámci tohoto rychlého startu:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Základní prostředky nástroje pro vyrovnávání zatížení vytvořené pro rychlý Start." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítače a nasadíte Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

#### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](/cli/azure/network/vnet#az_network_vnet_createt). Určete následující nastavení:

* Pojmenovaný **myVNet**
* Předpona adresy **10.1.0.0/16**
* Podsíť s názvem **myBackendSubnet**
* Předpona podsítě pro **10.1.0.0/24**
* Ve skupině prostředků **CreateIntLBQS-RG**
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

#### <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pomocí [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create) vytvoříte veřejnou IP adresu pro hostitele Azure bastionu. Určete následující nastavení:

* Vytvořte standardní zónu – redundantní veřejnou IP adresu s názvem **myBastionIP**
* V **CreateIntLBQS-RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Vytvoření podsítě Azure bastionu

Pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) vytvořte podsíť. Určete následující nastavení:

* Pojmenovaný **AzureBastionSubnet**
* Předpona adresy **10.1.1.0/24**
* Ve virtuální síti **myVNet**
* Ve skupině prostředků **CreateIntLBQS-RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Vytvoření hostitele služby Azure Bastion

K vytvoření hostitele použijte [AZ Network bastionu Create](/cli/azure/network/bastion#az_network_bastion_create) . Určete následující nastavení:

* Pojmenovaný **myBastionHost**
* V **CreateIntLBQS-RG**
* Přidruženo k veřejné IP **myBastionIP**
* Přidruženo k virtuální síti **myVNet**
* V umístění **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Nasazení hostitele Azure bastionu může trvat několik minut.

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

V případě standardního nástroje pro vyrovnávání zatížení zajistěte, aby vaše virtuální počítače měly síťová rozhraní, která patří do skupiny zabezpečení sítě. Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create). Určete následující nastavení:

* Pojmenovaný **myNSG**
* Ve skupině prostředků **CreateIntLBQS-RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Určete následující nastavení:

* Pojmenovaný **myNSGRuleHTTP**
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Protokol **(*)**
* Směr **příchozí**
* Zdroj **(*)**
* Cíl **(*)**
* Port cílového portu **80**
* Přístup **povolen**
* Priorita **200**

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

### <a name="create-back-end-servers"></a>Vytvoření back-end serverů

V této části vytvoříte:

* Tři síťová rozhraní pro virtuální počítače.
* Skupina dostupnosti pro virtuální počítače.
* Tři virtuální počítače, které se mají použít jako servery pro nástroj pro vyrovnávání zatížení.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Pomocí [AZ Network nic Create](/cli/azure/network/nic#az_network_nic_create)vytvořte tři síťová rozhraní. Určete následující nastavení:

* Pojmenované **myNicVM1**, **myNicVM2** a **myNicVM3**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Ve virtuální síti **myVNet**
* V podsíti **myBackendSubnet**
* V **myNSG** skupiny zabezpečení sítě

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Vytvoření skupiny dostupnosti pro virtuální počítače

Vytvořte skupinu dostupnosti pomocí [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az_vm_availability_set_create). Určete následující nastavení:

* Pojmenovaný **myAvailabilitySet**
* Ve skupině prostředků **CreateIntLBQS-RG**
* **Eastus** umístění

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítače pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Určete následující nastavení:

* Pojmenované **myVM1**, **myVM2** a **myVM3**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Připojeno k síťovému rozhraní **myNicVM1**, **myNicVM2** a **myNicVM3**
* **Win2019datacenter** image virtuálního počítače
* V **myAvailabilitySet**


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
Nasazení virtuálních počítačů může trvat několik minut.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

* Fond IP adres, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
* Druhý fond IP adres, ve kterém první fond odesílá síťový provoz s vyrovnáváním zatížení.
* Sonda stavu, která určuje stav instancí virtuálních počítačů.
* Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

#### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvořte veřejný Nástroj pro vyrovnávání zatížení pomocí [AZ Network](/cli/azure/network/lb#az_network_lb_create)diskont Create. Určete následující nastavení:

* Pojmenovaný **myLoadBalancer**
* Fond s názvem **myFrontEnd**
* Fond s názvem **myBackEndPool**
* Přidruženo k virtuální síti **myVNet**
* Přidruženo k podsíti **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Určete následující nastavení:

* Monitoruje stav virtuálních počítačů.
* Pojmenovaný **myHealthProbe**
* Protokol **TCP**
* **Port monitorování 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace protokolu IP pro příchozí provoz.
* Fond IP adres pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Určete následující nastavení:

* Pojmenovaný **myHTTPRule**
* Naslouchání na **portu 80** ve fondu **myFrontEnd**
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu adres **myBackEndPool** pomocí **portu 80** 
* Použití sondy stavu **myHealthProbe**
* Protokol **TCP**
* Časový limit nečinnosti **15 minut**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Přidání virtuálních počítačů do fondu nástroje pro vyrovnávání zatížení

Přidejte virtuální počítače do fondu back-end pomocí [AZ Network nic IP-config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Určete následující nastavení:

* Ve fondu adres **myBackEndPool**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Přidruženo k síťovému rozhraní **myNicVM1**, **myNicVM2** a **myNicVM3**
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---
## <a name="test-the-load-balancer"></a>Testování Load Balanceru

Vytvořte síťové rozhraní pomocí [AZ Network nic Create](/cli/azure/network/nic#az_network_nic_create). Určete následující nastavení:

* Pojmenovaný **myNicTestVM**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Ve virtuální síti **myVNet**
* V podsíti **myBackendSubnet**
* V **myNSG** skupiny zabezpečení sítě

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Vytvořte virtuální počítač pomocí [AZ VM Create](/cli/azure/vm#az_vm_create). Určete následující nastavení:

* Pojmenovaný **myTestVM**
* Ve skupině prostředků **CreateIntLBQS-RG**
* Připojeno k síťovému rozhraní **myNicTestVM**
* **Win2019Datacenter** image virtuálního počítače

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Možná budete muset několik minut počkat, než se virtuální počítač nasadí.

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Test

1. [Přihlaste se](https://portal.azure.com) na web Azure Portal.

2. Na stránce **Přehled** vyhledejte privátní IP adresu pro nástroj pro vyrovnávání zatížení. V nabídce na levé straně vyberte **všechny služby**  >  **všechny prostředky**  >  **myLoadBalancer**.

3. V přehledu **myLoadBalancer** zkopírujte adresu vedle **soukromé IP adresy**.

4. V nabídce na levé straně vyberte **všechny služby**  >  **všechny prostředky**. V seznamu prostředky ve skupině prostředků **CreateIntLBQS-RG** vyberte **myTestVM**.

5. Na stránce **Přehled** vyberte **připojit**  >  **bastionu**.

6. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

7. V **myTestVM** otevřete **Internet Explorer**.

8. Zadejte IP adresu z předchozího kroku do panelu Adresa v prohlížeči. Výchozí stránka webového serveru služby IIS se zobrazí v prohlížeči.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Snímek obrazovky s IP adresou v adresním řádku prohlížeče." border="true":::
   
Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače. Pak ručně aktualizujte webový prohlížeč z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už vaše prostředky nepotřebujete, odeberte ji pomocí příkazu [AZ Group Delete](/cli/azure/group#az_group_delete) a odeberte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Další kroky

Přehled nástroje Azure Load Balancer
> [!div class="nextstepaction"]
> [Co je Azure Load Balancer?](load-balancer-overview.md)
