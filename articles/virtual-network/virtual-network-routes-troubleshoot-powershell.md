---
title: Řešení potíží s trasy – prostředí PowerShell | Microsoft Docs
description: Informace o řešení potíží s trasy v modelu nasazení Azure Resource Manager pomocí Azure PowerShell.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 2d501419dde633f89a5760af9f82604006de6b6f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Řešení potíží s postupy pomocí prostředí Azure PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
> 
> 

Pokud dochází k problémům se síťovým připojením do nebo z vaší virtuální počítač Azure (VM), tras, které mohou ovlivňovat vaše datové přenosy virtuálních počítačů. Tento článek obsahuje přehled možností diagnostiky pro směrování, aby mohl pomoct řešit problémy s další.

Směrovací tabulky jsou spojeny s podsítěmi a platí na všech síťových rozhraní (NIC) v této podsíti. Každé rozhraní sítě můžete použít následující typy trasy:

* **Systémové trasy:** ve výchozím nastavení, má každá podsíť vytvořená ve virtuální síti Azure (VNet) systému směrovací tabulky, které umožňují místní provoz VNet, místní provoz prostřednictvím brány sítě VPN a přenosy z Internetu. Systémové trasy existovat také pro peered virtuální sítě.
* **Trasy protokolu BGP:** rozšíří na rozhraní sítě prostřednictvím ExpressRoute nebo VPN připojení site-to-site. Další informace o směrování protokolu BGP načtením [protokol BGP s bránami VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) a [přehled ExpressRoute](../expressroute/expressroute-introduction.md) články.
* **Trasy definované uživatelem (UDR):** Pokud používáte virtuální zařízení sítě nebo jsou vynucené tunelování provozu do místní sítě prostřednictvím sítě site-to-site VPN, může mít trasy definované uživatelem (udr) přidružené tabulky tras podsítě. Pokud si nejste obeznámeni s udr, přečtěte si [trasy definované uživatelem](virtual-networks-udr-overview.md#user-defined) článku.

S různými tras, které lze použít k síťovému rozhraní může být obtížné určit, které agregační trasy jsou platné. Pomoc při řešení potíží s připojení k síti virtuálních počítačů, můžete zobrazit všechny efektivní trasy pro rozhraní sítě v modelu nasazení Azure Resource Manager.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Řešení potíží s přenosy virtuálních počítačů pomocí efektivní trasy
Tento článek používá následující scénář jako příklad k ukazují, jak vyřešit efektivní trasy pro síťové rozhraní:

Virtuální počítač (*VM1*) připojené k virtuální síti (*VNet1*, předpona: 10.9.0.0/16) nepodaří připojit k VM(VM3) ve virtuální síti nově peered (*VNet3*, předpony 10.10.0.0/16). Neexistují žádné udr nebo BGP směruje u VM1 NIC1 síťové rozhraní připojené k virtuálnímu počítači, se použijí jenom systémové trasy.

Tento článek vysvětluje, jak a zjistěte příčinu selhání připojení pomocí funkce efektivní trasy v modelu nasazení správou prostředků Azure.
Tento příklad používá jenom systémové trasy, stejný postup slouží k určení selhání příchozí a odchozí připojení přes jakýkoli typ trasy.

> [!NOTE]
> Pokud virtuální počítač má více než jeden síťový adaptér připojený, zkontrolujte efektivní trasy pro jednotlivé síťové adaptéry diagnostikovat problémy s připojením k síti do a z virtuálního počítače.
> 
> 

### <a name="view-effective-routes-for-a-virtual-machine"></a>Zobrazit účinné postupy pro virtuální počítač
Agregace tras, které se použijí k virtuálnímu počítači najdete proveďte následující kroky:

### <a name="view-effective-routes-for-a-network-interface"></a>Zobrazit účinné postupy pro rozhraní sítě
Agregace tras, které se použijí k síťovému rozhraní najdete proveďte následující kroky:

1. Spuštění z relace prostředí Azure PowerShell a do Azure. Pokud si nejste obeznámeni s prostředím Azure PowerShell, přečtěte si [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) článku. Musí mít váš účet přiřazenou *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* operace pro síťové rozhraní. Zjistěte, jak přiřadit operations účty, najdete v tématu [vytvářet vlastní role pro řízení přístupu](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Následující příkaz vrátí všechny trasy použije k síťovému rozhraní s názvem *VM1 NIC1* ve skupině prostředků *RG1*.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Pokud neznáte název síťového rozhraní, zadejte následující příkaz pro načtení názvy všech síťových rozhraní v group.* prostředků
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   Tento výstup bude vypadat podobně jako výstup pro každý postup použije na podsíť, které síťový adaptér je připojen k:
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   Všimněte si následující výstup:
   
   * **Název**: název efektivní trasy, může být prázdný, pokud není výslovně uvedeno, pro trasy definované uživatelem. 
   * **Stav**: označuje stav efektivní trasy. Možné hodnoty jsou "Aktivní" nebo "Neplatná"
   * **Adres**: Určuje předpona adresy efektivní trasy v notaci CIDR. 
   * **nextHopType**: označuje dalšího přechodu pro danou trasu. Možné hodnoty jsou *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, nebo *Null*. Hodnota *Null* pro **nextHopType** UDR může znamenat neplatná. Například pokud **nextHopType** je *VirtualAppliance* a virtuální zařízení sítě virtuálního počítače není zřízený nebo spuštěném stavu. Pokud **nextHopType** je *Brána VPN* a neexistuje žádná brána zřízený/běží v dané virtuální síti, může zneplatní trasy.
   * **NextHopIpAddress**: Určuje IP adresu dalšího přechodu efektivní trasy.
   
   Následující příkaz vrátí trasy v snazší zobrazení tabulky:
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   Následující výstup je některé výstup, přijal scénář popsaný dříve:
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. Neexistuje žádná trasa uvedené *WestUS VNet3* virtuální síť (předpony 10.10.0.0/16)** z *WestUS VNet1* (předpony 10.9.0.0/16) ve výstupu z předchozího kroku. Jak je znázorněno na následujícím obrázku, virtuální síť partnerského vztahu propojení *WestUS VNet3* virtuální sítě je v *odpojeno* stavu.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    Obousměrná odkaz pro partnerského vztahu se přeruší, která vysvětluje, proč VM1 se nemohl připojit k VM3 v *WestUS VNet3* virtuální sítě. Instalační program partnerského vztahu odkaz obousměrný virtuální síť znovu pro *WestUS VNet1* a *WestUS VNet3* virtuální sítě. Výstup po správně vytvoření partnerského vztahu propojení virtuální sítě zahrnuje:
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    Po určení problém, můžete přidat, odebrat, nebo změňte trasy a směrovacích tabulek. Pomocí následujícího příkazu zobrazte seznam příkazů určených k tomu:
   
        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Požadavky
Pokud kontrola seznam tras vrátí mějte na paměti několik akcí:

* Směrování je založena na nejdelší shody předpony (LPM) mezi udr, směrování protokolu BGP a systému. Pokud existuje víc tras se stejnou shodou LPM, pak trasa se vybere na základě původu v následujícím pořadí:
  
  * Trasa definovaná uživatelem
  * Trasa protokolu BGP
  * Trasy systému (výchozí)
    
    S efektivní směrování uvidí jenom účinné postupy, které jsou založené na všechny dostupné trasy shodou LPM. Ukazuje, jak se ve skutečnosti vyhodnocují trasy pro daný síťový adaptér, díky tomu mnohem snazší řešení potíží s konkrétní trasy, které mohou ovlivňovat připojení z virtuálního počítače.
* Pokud máte udr a odesílání provozu pro virtuální síťové zařízení (hodnocení chyb zabezpečení), s *VirtualAppliance* jako **nextHopType**, ujistěte se, zda je na hodnocení chyb zabezpečení, který provoz přijímá povolena předávání IP nebo dojde ke ztrátě paketů. 
* Pokud vynuceném tunelovém propojení je povoleno, veškerý odchozí internetový provoz se směruje na místní. Připojení RDP/SSH z Internetu do virtuální počítač nemusí fungovat s tímto nastavením, v závislosti na tom, jak místní zpracovává tento provoz. 
  Můžete povolit vynucené tunelování:
  * Pokud připojení site-to-site VPN pomocí nastavení trasy definované uživatelem (UDR) s nextHopType jako brány sítě VPN
  * Pokud je výchozí trasy inzerované přes protokol BGP
* Pro virtuální síť partnerského vztahu provoz fungovala správně, systémová trasa s **nextHopType** *VNetPeering* peered VNet předponu rozsahu, musí existovat. Pokud tyto trasy neexistuje a partnerského vztahu propojení virtuální sítě vypadá v pořádku:
  * Počkejte několik sekund a opakovat, pokud se jedná o nově vytvořeným partnerského vztahu odkaz. Někdy trvá déle šířil trasy na všech síťových rozhraní v podsíti.
  * Skupina zabezpečení sítě (NSG) pravidla, které mohou ovlivňovat tok provozu. Další informace najdete v tématu [odstraňování skupin zabezpečení sítě](virtual-network-nsg-troubleshoot-powershell.md) článku.

