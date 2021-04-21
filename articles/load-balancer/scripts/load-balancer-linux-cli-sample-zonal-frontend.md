---
title: Vyrovnávání zatížení virtuálních počítačů v rámci zóny – Azure CLI
description: Tento ukázkový skript Azure CLI provádí vyrovnávání zatížení provozu na virtuálních počítačích v rámci konkrétní zóny dostupnosti
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a8ce3f52005b349c266381da599ebe09c866fce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788722"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-within-a-specific-availability-zone"></a>Ukázkový skript Azure CLI: Vyrovnávání zatížení provozu na virtuální počítače v rámci konkrétní zóny dostupnosti

Tento ukázkový skript Azure CLI vytvoří vše potřebné pro spuštění několika virtuálních počítačů s Ubuntu v konfiguraci s vysokou dostupností a vyrovnáváním zatížení v rámci konkrétní zóny dostupnosti. Po spuštění skriptu budete mít v jedné zóně dostupnosti v rámci oblasti tři virtuální počítače, které budou přístupné přes službu Azure Standard Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

```azurecli-interactive
  #!/bin/bash

  # Create a resource group.
   az group create \
    --name myResourceGroup \
    --location westeurope

  # Create a virtual network.
   az network vnet create \
    --resource-group myResourceGroup \
    --location westeurope \
    --name myVnet \
    --subnet-name mySubnet

  # Create a zonal Standard public IP address.
   az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard
    --zone 1

  # Create an Azure Load Balancer.
   az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --sku Standard

  # Creates an LB probe on port 80.
   az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80

  # Creates an LB rule for port 80.
   az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRuleWeb \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe

  # Create three NAT rules for port 22.
   for i in `seq 1 3`; do
    az network lb inbound-nat-rule create \
     --resource-group myResourceGroup \
     --lb-name myLoadBalancer \
     --name myLoadBalancerRuleSSH$i \
     --protocol tcp \
     --frontend-port 422$i \
     --backend-port 22 \
     --frontend-ip-name myFrontEndPool
   done

  # Create a network security group
   az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup

  # Create a network security group rule for port 22.
   az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*'  \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 1000

  # Create a network security group rule for port 80.
    az network nsg rule create \
     --resource-group myResourceGroup \
     --nsg-name myNetworkSecurityGroup \
     --name myNetworkSecurityGroupRuleHTTP \
     --protocol tcp \
     --direction inbound \
     --source-address-prefix '*' \
     --source-port-range '*' \
     --destination-address-prefix '*' \
     --destination-port-range 80 \
     --access allow \
     --priority 2000

  # Create three virtual network cards and associate with public IP address and NSG.
  for i in `seq 1 3`; do
   az network nic create \
     --resource-group myResourceGroup \
     --name myNic$i \
     --vnet-name myVnet \
     --subnet mySubnet \
     --network-security-group myNetworkSecurityGroup \
     --lb-name myLoadBalancer \
     --lb-address-pools myBackEndPool \
     --lb-inbound-nat-rules myLoadBalancerRuleSSH$i
  done

# Create three virtual machines, this creates SSH keys if not present.
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroup \
    --name myVM$i \
    --zone 1 \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
done

```

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače, skupiny dostupnosti, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a podsíť. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu se statickou IP adresou a přidruženým názvem DNS. |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Vytvoří nástroj pro vyrovnávání zatížení Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | Vytvoří sondu nástroje pro vyrovnávání zatížení. Sonda nástroje pro vyrovnávání zatížení se používá k monitorování jednotlivých virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. Pokud nějaký virtuální počítač přestane být přístupný, nebude se do něj směrovat provoz. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Vytvoří pravidlo nástroje pro vyrovnávání zatížení. V této ukázce se vytvoří pravidlo pro port 80. Když nástroj pro vyrovnávání zatížení přijme přenos HTTP, přesměruje ho na port 80 některého z virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Vytvoří pravidlo překladu adres (NAT) nástroje pro vyrovnávání zatížení.  Pravidla NAT mapují port nástroje pro vyrovnávání zatížení na port na virtuálním počítači. V této ukázce se vytvoří pravidlo NAT pro provoz SSH do všech virtuálních počítačů v sadě nástroje pro vyrovnávání zatížení.  |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Vytvoří skupinu zabezpečení sítě (NSG), což je hranice zabezpečení mezi internetem a virtuálním počítačem. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Vytvoří pravidlo NSG, které povolí příchozí provoz. V této ukázce se otevře port 22 pro provoz SSH. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Vytvoří virtuální síťovou kartu a připojí ji k virtuální síti, podsíti a skupině NSG. |
| [az vm create](/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině NSG. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty CLI pro sítě Azure najdete v [dokumentaci k sítím Azure](../cli-samples.md).