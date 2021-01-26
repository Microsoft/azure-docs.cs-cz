---
title: Aktualizace nebo odstranění stávajícího Azure Load Balanceru využívaného škálovací sadou virtuálních počítačů
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: S tímto článkem se naučíte začít s Azure Standard Load Balancer a Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790085"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Jak aktualizovat nebo odstranit Azure Load Balancer používané v Virtual Machine Scale Sets

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Jak nastavit Azure Load Balancer pro horizontální navýšení kapacity Virtual Machine Scale Sets
  * Ujistěte se, že je u Load Balancer nastaven [fond příchozích adres NAT](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) a že sada škálování virtuálního počítače je vložená do back-endu fondu v Load Balancer. Když se do sady škálování virtuálního počítače přidají nové instance virtuálních počítačů, Azure Load Balancer se automaticky vytvoří nová pravidla příchozího překladu adres (NAT) ve fondu příchozích překladů adres (NAT). 
  * Pokud chcete zjistit, jestli je příchozí fond NAT správně nastavený, 
  1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
  
  1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
  
  1. V části **Nastavení** vyberte **pravidla příchozího překladu adres (NAT)**.
Pokud se zobrazí v pravém podokně, zobrazí se seznam pravidel vytvořených pro každou jednotlivou instanci v sadě škálování virtuálního počítače. to všechno je nastavené, aby bylo možné kdykoli přejít k horizontálnímu škálování.

## <a name="how-to-add-inbound-nat-rules"></a>Jak přidat pravidla pro příchozí překlad adres (NAT)? 
  * Individuální příchozí pravidlo NAT nelze přidat. Můžete ale přidat sadu pravidel příchozího překladu adres (NAT) s definovaným rozsahem portů front-endu a back-end port pro všechny instance v sadě škálování virtuálního počítače.
  * Pokud chcete pro Virtual Machine Scale Sets přidat celou sadu pravidel příchozího překladu adres (NAT), musíte nejdřív v Load Balancer vytvořit fond příchozího překladu adres (NAT) a pak na fond příchozího překladu adres (NAT) odkázat ze síťového profilu sady škálování virtuálních počítačů. Úplný příklad použití rozhraní příkazového řádku je uveden níže.
  * Nový fond příchozího překladu adres (NAT) by neměl mít překrývající se rozsah portů front-endu se stávajícími vstupními fondy NAT Pokud chcete zobrazit nastavené stávající fondy NAT, můžete použít tento [příkaz CLI](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list) .
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>Jak aktualizovat pravidla příchozího překladu adres (NAT)? 
  * Individuální příchozí pravidlo NAT nelze aktualizovat. Můžete ale aktualizovat sadu příchozích pravidel NAT s definovaným rozsahem portů front-endu a back-end port pro všechny instance v sadě škálování virtuálního počítače.
  * Aby bylo možné aktualizovat celou sadu příchozích pravidel NAT pro Virtual Machine Scale Sets, je nutné v Load Balancer aktualizovat fond příchozího překladu adres (NAT). 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Jak odstranit pravidla příchozího překladu adres (NAT)? 
* Individuální příchozí pravidlo NAT nelze odstranit. Můžete ale odstranit celou sadu pravidel příchozího překladu adres (NAT).
* Aby bylo možné odstranit celou sadu příchozích pravidel NAT používaných sadou škálování, je třeba nejprve odebrat fond NAT ze sady škálování. Úplný příklad použití rozhraní příkazového řádku je uveden níže:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Postup přidání více konfigurací protokolu IP:
1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V části **Nastavení** vyberte **Konfigurace IP adresy front-endu** a pak vyberte **Přidat**.
   
1. Na stránce **Přidat IP adresu front-endu** zadejte hodnoty a vyberte **OK** .

1. Pokud jsou potřeba nová pravidla vyrovnávání zatížení, postupujte podle [kroků 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) a [Krok 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) v tomto kurzu.

1. V případě potřeby vytvořte novou sadu příchozích pravidel NAT pomocí nově vytvořených konfigurací IP adresy front-endu. Příklad najdete tady v [předchozí části].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Jak odstranit konfiguraci IP adresy front-endu, kterou používá sada škálování virtuálních počítačů: 
 1. Pokud chcete odstranit konfiguraci IP adresy front-endu, kterou používá sada škálování, musíte nejdřív odstranit fond příchozího překladu adres (NAT) (sadu pravidel příchozího překladu adres) odkazující na konfiguraci IP adresy front-endu. Pokyny, jak odstranit příchozí pravidla, najdete v předchozí části.
 1. Odstraňte pravidlo vyrovnávání zatížení odkazující na konfiguraci IP adresy front-endu. 
 1. Odstraňte konfiguraci IP adresy front-endu.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Jak odstranit Azure Load Balancer používané sadou škálování virtuálních počítačů: 
 1. Pokud chcete odstranit konfiguraci IP adresy front-endu, kterou používá sada škálování, musíte nejdřív odstranit fond příchozího překladu adres (NAT) (sadu pravidel příchozího překladu adres) odkazující na konfiguraci IP adresy front-endu. Pokyny, jak odstranit příchozí pravidla, najdete v předchozí části.
 
 1. Odstraňte pravidlo vyrovnávání zatížení odkazující na back-end fond, který obsahuje sadu škálování virtuálního počítače.
 
 1. Odeberte odkaz loadBalancerBackendAddressPool ze síťového profilu sady škálování virtuálního počítače. Úplný příklad použití rozhraní příkazového řádku je uveden níže:
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
Nakonec odstraňte prostředek Load Balancer.
 
## <a name="next-steps"></a>Další kroky

Další informace o Azure Load Balancer a sadě škálování virtuálních počítačů najdete v článku o konceptech.

> [Azure Load Balancer se službou Azure Virtual Machine Scale Sets](load-balancer-standard-virtual-machine-scale-sets.md)