---
title: Aktualizace nebo odstranění existujícího nástroje pro vyrovnávání zatížení, který používá služba Virtual Machine Scale Sets
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: S tímto článkem se naučíte začít používat službu Azure Standard Load Balancer a službu Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 952889777e4236d7fa03fad5b1bdbf98499f7066
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721306"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Aktualizace nebo odstranění nástroje pro vyrovnávání zatížení používaného službou Virtual Machine Scale Sets

Když pracujete se sadami škálování virtuálních počítačů a instancí Azure Load Balancer, můžete:

- Přidání, aktualizace a odstranění pravidel.
- Přidejte konfigurace.
- Odstraňte Nástroj pro vyrovnávání zatížení.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Nastavení nástroje pro vyrovnávání zatížení pro škálování služby Virtual Machine Scale Sets

Ujistěte se, že je v instanci Azure Load Balancer nastaven [fond příchozích adres NAT](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) a že sada škálování virtuálního počítače je vložená do back-endu fondu nástroje pro vyrovnávání zatížení. Když se do sady škálování virtuálního počítače přidají nové instance virtuálních počítačů, Load Balancer se automaticky vytvoří nová pravidla příchozího překladu adres (NAT) ve fondu příchozích překladů adres (NAT).

Chcete-li ověřit, zda je příchozí fond NAT správně nastavený:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **všechny prostředky**. Pak v seznamu prostředků vyberte **MyLoadBalancer** .
1. V části **Nastavení** vyberte **pravidla příchozího překladu adres (NAT)**. Pokud se v pravém podokně zobrazí seznam pravidel vytvořených pro každou jednotlivou instanci v sadě škálování virtuálního počítače, je vše nastavené na škálování kdykoli.

## <a name="add-inbound-nat-rules"></a>Přidat pravidla příchozího překladu adres (NAT)

Jednotlivá příchozí pravidla překladu adres (NAT) se nedají přidat. Můžete ale přidat sadu pravidel příchozího překladu adres (NAT) s definovaným rozsahem portů front-endu a back-end port pro všechny instance v sadě škálování virtuálního počítače.

Pokud chcete pro sadu škálování virtuálního počítače přidat celou sadu pravidel příchozího překladu adres (NAT), v nástroji pro vyrovnávání zatížení nejprve vytvořte fond příchozího překladu adres (NAT). Pak na příchozí fond NAT odkázat ze síťového profilu sady škálování virtuálních počítačů. Zobrazí se úplný příklad použití rozhraní příkazového řádku.

Nový fond příchozího překladu adres (NAT) by neměl mít překrývající se rozsah portů front-endu se stávajícími příchozími fondy NAT. Pokud chcete zobrazit existující příchozí fondy NAT, které jsou nastavené, použijte tento [příkaz rozhraní příkazového řádku](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list):
  
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
## <a name="update-inbound-nat-rules"></a>Aktualizovat příchozí pravidla NAT

Jednotlivá pravidla příchozího překladu adres (NAT) nejde aktualizovat. Můžete ale aktualizovat sadu příchozích pravidel NAT s definovaným rozsahem portů front-endu a back-end port pro všechny instance v sadě škálování virtuálního počítače.

Pokud chcete aktualizovat celou sadu příchozích pravidel NAT pro Virtual Machine Scale Sets, aktualizujte ve službě Load Balancer příchozí fond NAT.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Odstranit příchozí pravidla NAT

Jednotlivá pravidla příchozího překladu adres (NAT) nejde odstranit, ale můžete odstranit celou sadu pravidel příchozího překladu adres (NAT) tak, že odstraníte fond příchozích adres (NAT).

Pokud chcete fond NAT odstranit, odeberte ho nejdřív ze sady škálování. Úplný příklad použití rozhraní příkazového řádku je uveden zde:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Přidání více konfigurací IP

Přidání více konfigurací protokolu IP:

1. V nabídce vlevo vyberte **všechny prostředky**. Pak v seznamu prostředků vyberte **MyLoadBalancer** .
1. V části **Nastavení** vyberte **Konfigurace IP adresy front-endu**. Pak vyberte **Přidat**.
1. Na stránce **Přidat IP adresu front-endu** zadejte hodnoty a vyberte **OK**.
1. Pokud jsou potřeba nová pravidla vyrovnávání zatížení, postupujte podle [kroků 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) a [Krok 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) v tomto kurzu.
1. V případě potřeby vytvořte novou sadu pravidel příchozího překladu adres (NAT) pomocí nově vytvořených konfigurací front-end IP adres. Příklad najdete v předchozí části.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Více Virtual Machine Scale Sets za jedním Load Balancer

Vytvořte ve službě Load Balancer příchozí fond NAT, odkazujte na fond příchozích adres NAT v síťovém profilu sady škálování virtuálních počítačů a nakonec aktualizujte instance, aby se změny projevily. Opakujte kroky pro všechny Virtual Machine Scale Sets.

Nezapomeňte vytvořit samostatné fondy příchozích adres NAT s nepřekrývajícím se rozsahy portů front-endu.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Odstranit konfiguraci front-endové IP adresy používané sadou škálování virtuálního počítače

Postup odstranění konfigurace front-endové IP adresy, kterou používá sada škálování:

 1. Nejdřív odstraňte fond příchozího překladu adres (příchozí pravidla NAT), který odkazuje na konfiguraci front-endu IP adresy. Pokyny, jak odstranit příchozí pravidla, najdete v předchozí části.
 1. Odstraňte pravidlo vyrovnávání zatížení, které odkazuje na konfiguraci front-endové IP adresy.
 1. Odstraňte konfiguraci front-end IP adresy.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Odstranění nástroje pro vyrovnávání zatížení, který používá sada škálování virtuálního počítače

Postup odstranění konfigurace front-endové IP adresy, kterou používá sada škálování:

 1. Nejdřív odstraňte fond příchozího překladu adres (příchozí pravidla NAT), který odkazuje na konfiguraci front-endu IP adresy. Pokyny, jak odstranit příchozí pravidla, najdete v předchozí části.
 1. Odstraňte pravidlo vyrovnávání zatížení, které odkazuje na fond back-end, který obsahuje sadu škálování virtuálního počítače.
 1. Odeberte `loadBalancerBackendAddressPool` odkaz ze síťového profilu sady škálování virtuálních počítačů.
 
 Úplný příklad použití rozhraní příkazového řádku je uveden zde:

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
Nakonec odstraňte prostředek nástroje pro vyrovnávání zatížení.
 
## <a name="next-steps"></a>Další kroky

Další informace o Azure Load Balancer a službě Virtual Machine Scale Sets najdete v tématu věnovaném konceptům.

> [Azure Load Balancer se službou Virtual Machine Scale Sets](load-balancer-standard-virtual-machine-scale-sets.md)
