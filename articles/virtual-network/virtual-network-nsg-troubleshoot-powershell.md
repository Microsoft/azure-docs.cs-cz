---
title: Řešení potíží s skupin zabezpečení sítě - prostředí PowerShell | Microsoft Docs
description: Informace o řešení potíží s skupin zabezpečení sítě v modelu nasazení Azure Resource Manager pomocí Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 6beaeb4b7e5c9e393427d575f1cf8bc48599dbd5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Řešení potíží s skupin zabezpečení sítě pomocí prostředí Azure PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Pokud jste nakonfigurovali skupiny zabezpečení sítě (Nsg) ve virtuálním počítači (VM) a dochází problémy s připojením k virtuální počítač, tento článek obsahuje přehled možností diagnostiky pro skupiny Nsg k další řešení.

Skupiny Nsg umožní určit typy provozu s tokem do aplikace a z virtuálních počítačů (VM). Skupiny Nsg můžete použít na podsítě ve virtuální síti Azure (VNet), síťová rozhraní (NIC) nebo obojí. Efektivní pravidla použít na síťový adaptér jsou agregace pravidla, které existují v skupin Nsg použije k síťovému adaptéru a podsíť, ke kterému je připojen k. Pravidla v rámci těchto skupin Nsg můžete někdy vzájemném konfliktu a mít vliv na připojení k síti Virtuálního počítače.  

Můžete zobrazit všechna pravidla efektivní zabezpečení od vaší skupiny Nsg použije na síťové adaptéry Virtuálního počítače. Tento článek ukazuje, jak vyřešit problémy s připojením k virtuální počítač pomocí těchto pravidel v modelu nasazení Azure Resource Manager. Pokud si nejste obeznámeni s virtuální sítí a NSG koncepty, přečtěte si [virtuální síť](virtual-networks-overview.md) a [skupin zabezpečení sítě](virtual-networks-nsg.md) přehled články.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Řešení potíží s přenosy virtuálních počítačů pomocí efektivní pravidla zabezpečení
Scénář, který následuje je příkladem častých problémů připojení:

Virtuální počítač s názvem *VM1* je součástí podsíť s názvem *Subnet1* v rámci virtuální sítě s názvem *WestUS VNet1*. Pokus o připojení k virtuálnímu počítači pomocí protokolu RDP přes TCP port 3389 selže. Skupiny Nsg jsou použity na síťový adaptér *VM1 NIC1* a podsíť *Subnet1*. Provoz TCP port 3389 je povolený v této skupině související se síťovým rozhraním *VM1 NIC1*, ale příkaz ping TCP a VM1 je port 3389 selže.

Při tomto příkladu používá TCP port 3389, následující postup slouží k určení selhání příchozí a odchozí připojení přes libovolný port.

## <a name="detailed-troubleshooting-steps"></a>Podrobné kroky řešení potíží
Pomocí následujících kroků pro řešení potíží s skupiny Nsg pro virtuální počítač:

1. Spuštění z relace prostředí Azure PowerShell a do Azure. Pokud si nejste obeznámeni s používáním Azure PowerShell, přečtěte si [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) článku. Musí mít váš účet přiřazenou *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* operace pro síťové rozhraní. Zjistěte, jak přiřadit operations účty, najdete v tématu [vytvářet vlastní role pro řízení přístupu](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Zadejte následující příkaz, který vrátí všechna pravidla NSG použije na síťový adaptér s názvem *VM1 NIC1* ve skupině prostředků *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Pokud neznáte název síťového adaptéru, zadejte následující příkaz pro načtení názvy všech síťových adaptérů ve skupině prostředků: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    Tento text je ukázkový výstup efektivní pravidla pro *VM1 NIC1* síťovou kartu:
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Vezměte na vědomí následující informace ve výstupu:
   
   * Existují dva **skupinu zabezpečení sítě** částech: jeden je přidružená k podsíti (*Subnet1*) a jeden síťový adaptér přidružený (*VM1 NIC1*). V tomto příkladu má byla skupina NSG použitá pro každé.
   * **Přidružení** ukazuje prostředků (podsíti nebo síťové KARTĚ) je přidružen daný NSG. Pokud prostředek NSG je přesunut nebo zrušit přidružení bezprostředně před spuštěním tohoto příkazu, možná budete muset Počkejte několik sekund pro změnu tak, aby odrážela ve výstupu příkazu. 
   * Názvy pravidel, které začínají *defaultSecurityRules*: když NSG se vytvoří, jsou v něm vytvořit několik výchozí pravidla zabezpečení. Výchozí pravidla nelze odebrat, ale je možné přepsat s vyšší prioritou pravidla.
     Pro čtení [NSG přehled](virtual-networks-nsg.md#default-rules) článku se dozvíte další informace o NSG výchozí pravidla zabezpečení.
   * **ExpandedAddressPrefix** rozšíří předpony adres pro NSG výchozí značky. Značky představují více předponami adresy. Rozšíření značek může být užitečné při řešení potíží s připojením virtuálního počítače z konkrétní předpony. S partnerský vztah virtuální sítě, například značky VIRTUAL_NETWORK rozbalí a zobrazí peered předponami virtuální sítě v předchozí výstup.
     
     > [!NOTE]
     > Příkaz pouze ukazuje efektivní pravidla Pokud je skupina NSG přidružená podsíť, síťový adaptér nebo obojí. Virtuální počítač může mít více síťových adaptérů se použít odlišné skupiny Nsg. Při odstraňování problémů s, spusťte příkaz pro každý síťový adaptér.
     > 
     > 
3. K usnadnění filtrování přes větší počet pravidel NSG, zadejte následující příkazy a pokračovat v řešení potíží: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Filtr pro provoz protokolu RDP (TCP port 3389), je použít k zobrazení mřížky, jak je znázorněno na následujícím obrázku:
   
    ![Seznam pravidel](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Jak můžete vidět v zobrazení mřížky, jsou obě povolit a zakázat pravidla pro protokol RDP. Zobrazuje výstup z kroku 2, který *DenyRDP* pravidlo je v této skupině se použije na podsíť. Pro příchozí pravidla Nsg použije na podsíť jsou zpracován jako první. Pokud je nalezena shoda, skupina NSG použitá na síťové rozhraní není zpracován. V takovém případě *DenyRDP* pravidlo z podsítě blokuje protokolu RDP na virtuální počítač (**VM1**).
   
   > [!NOTE]
   > Virtuální počítač může mít několik síťových adaptérů, které jsou k němu připojen. Každý může být připojena k jiné podsíti. Vzhledem k tomu, že příkazy v předchozích krocích se spouštějí na síťový adaptér, je důležité zajistit, že zadáváte dochází k selhání připojení pro síťový adaptér. Pokud si nejste jisti, můžete vždy spustit příkazy pro každou síťovou kartu připojenou k virtuálnímu počítači.
   > 
   > 
5. Chcete-li do VM1 protokolu RDP, změňte *odepřít protokolu RDP (3389)* pravidlo pro *povolit RDP(3389)* v **Subnet1 NSG** NSG. Potvrďte, že otevřete otevřením připojení ke vzdálené ploše k virtuálnímu počítači nebo pomocí nástroje Pspingu TCP port 3389. Další informace o Pspingu načtením [stránky pro stažení Pspingu](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Můžete nebo odebrání pravidla skupinu NSG podle informací uvedených v následující příkaz z:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Požadavky
Při řešení potíží s připojením problémy, zvažte následující body:

* Výchozí pravidla NSG se blokují příchozí přístup z Internetu a pouze povolit virtuální síť příchozí přenosy. Pravidla musí být přidané do povolit příchozí přístup z Internetu, podle potřeby.
* Pokud neexistují žádná pravidla zabezpečení NSG způsobuje připojení k síti Virtuálního počítače k selhání, může být problém z důvodu:
  * Software brány firewall běžící v rámci operačního systému Virtuálního počítače
  * Trasy nakonfigurované pro virtuální zařízení nebo místní provoz. Internetové přenosy můžete přesměrovat do místního prostřednictvím vynucené tunelování. Připojení RDP/SSH z Internetu virtuální počítač nemusí fungovat s tímto nastavením, v závislosti na tom, jak místní síťový hardware zpracovává tento provoz. Pro čtení [řešení potíží s trasy](virtual-network-routes-troubleshoot-powershell.md) článku se dozvíte, jak k diagnostikování problémů trasy, které může být zpomalovat toku provozu a odhlásit z virtuálního počítače. 
* Pokud máte peered virtuálních sítí, ve výchozím nastavení, se automaticky rozšíří VIRTUAL_NETWORK značky pro zahrnutí předpony peered virtuální sítě. Tyto předpony v můžete zobrazit **ExpandedAddressPrefix** seznamu, vyřešte všechny problémy související s konektivitou partnerského vztahu virtuální sítě. 
* Efektivní zabezpečení pravidla se zobrazují pouze pokud je skupina NSG přidružená Virtuálního počítače síťový adaptér a nebo podsíť. 
* Pokud neexistují žádné skupiny Nsg přidružená síťový adaptér nebo podsíť a jestli máte veřejné IP adresy přiřazené k virtuálnímu počítači, budou všechny porty otevřené pro příchozí a odchozí přístup. Pokud virtuální počítač má veřejnou IP adresu, použijete skupiny Nsg na síťový adaptér nebo podsíť důrazně doporučujeme.  

