---
title: 'Azure ExpressRoute: Tabulky ARP – řešení potíží'
description: Tato stránka obsahuje pokyny k získání tabulek ARP pro okruh ExpressRoute
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: b31e9eb852c69d5f02eb855e319d2a4901942994
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618521"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Získání tabulek ARP v modelu nasazení Správce prostředků
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede kroky, jak se naučit tabulky ARP pro okruh ExpressRoute.

> [!IMPORTANT]
> Tento dokument je určen k diagnostice a opravě jednoduchých problémů. Není určena jako náhrada za podporu společnosti Microsoft. Pokud se vám nedaří problém vyřešit pomocí níže popsaných pokynů, je nutné otevřít lístek podpory s [podporou společnosti Microsoft.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabulky protokolu ARP (Address Resolution Protocol) a ARP
Protokol ARP (Address Resolution Protocol) je protokol vrstvy 2 definovaný v [protokolu RFC 826](https://tools.ietf.org/html/rfc826). ARP se používá k mapování ethernetové adresy (MAC adresy) s IP adresou.

Tabulka ARP obsahuje mapování adresy ipv4 a adresy MAC pro konkrétní partnerský vztah. Tabulka ARP pro partnerský vztah okruhu ExpressRoute poskytuje následující informace pro každé rozhraní (primární a sekundární)

1. Mapování ip adresy rozhraní místního routeru na MAC adresu
2. Mapování IP adresy rozhraní směrovače ExpressRoute na adresu MAC
3. Stáří mapování

Tabulky ARP mohou pomoci ověřit konfiguraci vrstvy 2 a řešit problémy se základním připojením vrstvy 2. 

Příklad tabulky ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Následující část obsahuje informace o tom, jak můžete zobrazit tabulky ARP, které vidí hraniční směrovače ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Předpoklady pro učení Tabulek ARP
Ujistěte se, že máte následující, než budete postupovat dále

* Platný okruh ExpressRoute nakonfigurovaný s alespoň jedním partnerským vztahem. Okruh musí být plně nakonfigurován poskytovatelem připojení. Vy (nebo váš poskytovatel připojení) musíte mít nakonfigurované alespoň jeden z partnerských styků (Azure private, Azure public a Microsoft) v tomto okruhu.
* Rozsahy IP adres používané pro konfiguraci partnerských uživatelů (Azure private, Azure public a Microsoft). Projděte si příklady přiřazení IP adres na [stránce požadavky na směrování ExpressRoute,](expressroute-routing.md) abyste získali přehled o tom, jak jsou ip adresy mapovány na rozhraní na vaší straně a na straně ExpressRoute. Informace o konfiguraci partnerského vztahu můžete získat kontrolou [stránky konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-arm.md).
* Informace od vašeho síťového týmu / poskytovatele připojení na MAC adresy rozhraní používaných s těmito IP adresami.
* Musíte mít nejnovější modul PowerShellu pro Azure (verze 1.50 nebo novější).

> [!NOTE]
> Pokud je vrstva 3 poskytována poskytovatelem služeb a tabulky ARP jsou prázdné v níže uvedeném portálu nebo výstupu, aktualizujte konfiguraci okruhu pomocí tlačítka aktualizace na portálu. Tato operace bude mít na okruhu správnou konfiguraci směrování. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Získání tabulek ARP pro okruh ExpressRoute
Tato část obsahuje pokyny, jak můžete zobrazit tabulky ARP na partnerský vztah pomocí prostředí PowerShell. Vy nebo váš poskytovatel připojení musíte nakonfigurovat partnerský vztah před dalším postupováním. Každý okruh má dvě cesty (primární a sekundární). Tabulku ARP můžete zkontrolovat pro každou cestu nezávisle.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro privátní partnerský vztah Azure
Následující rutina poskytuje tabulky ARP pro privátní partnerský vztah Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Ukázkový výstup je uveden níže pro jednu z cest

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabulky ARP pro veřejný partnerský vztah Azure
Následující rutina poskytuje tabulky ARP pro veřejný partnerský vztah Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Ukázkový výstup je uveden níže pro jednu z cest

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabulky ARP pro partnerský vztah Microsoftu
Následující rutina obsahuje tabulky ARP pro partnerský vztah Microsoftu

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Ukázkový výstup je uveden níže pro jednu z cest

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak tyto informace používat
Tabulku ARP partnerského vztahu lze použít k určení ověření konfigurace vrstvy 2 a připojení. Tato část obsahuje přehled o tom, jak budou tabulky ARP vypadat v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabulka ARP, když je obvod v provozním stavu (očekávaný stav)
* Tabulka ARP bude mít položku pro místní stranu s platnou IP adresou a adresou MAC a podobnou položkou na straně Microsoft. 
* Poslední oktet místní IP adresy bude vždy liché číslo.
* Poslední oktet adresy IP společnosti Microsoft bude vždy sudé číslo.
* Stejná adresa MAC se zobrazí na straně Microsoftu pro všechny 3 partnerské vztahy (primární / sekundární). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabulka ARP, když má problémy na straně místního poskytovatele připojení / zprostředkovatele připojení
Pokud se objeví problémy s místním poskytovatelem připojení nebo poskytovatelem připojení, může se zobrazit, že se v tabulce ARP zobrazí pouze jedna položka nebo místní adresa MAC bude neúplná. Zobrazí se mapování mezi adresou MAC a adresou IP použitou na straně společnosti Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

– nebo –
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Otevřete žádost o podporu s poskytovatelem připojení k ladění těchto problémů. Pokud tabulka ARP nemá IP adresy rozhraní mapovaných na adresy MAC, přečtěte si následující informace:
> 
> 1. Pokud je na rozhraní MSEE-PR použita první IP adresa podsítě /30 přiřazená pro spojení mezi MSEE-PR a MSEE. Azure vždy používá druhou IP adresu pro msees.
> 2. Ověřte, zda značky VLAN zákazníka (C-Tag) a služby (S-Tag) odpovídají dvojici MSEE-PR i MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabulka ARP, pokud má problémy na straně microsoftu
* Pokud se na straně microsoftu objeví tabulka ARP pro partnerský vztah, nezobrazí se tabulka ARP. 
* Otevřete lístek podpory s [podporou společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Určete, že máte problém s připojením vrstvy 2. 

## <a name="next-steps"></a>Další kroky
* Ověření konfigurací vrstvy 3 pro okruh ExpressRoute
  * Získání souhrnu trasy k určení stavu relací protokolu BGP 
  * Získání směrovací tabulky k určení, které předpony jsou inzerovány v rámci ExpressRoute
* Ověření přenosu dat kontrolou bajtů dovnitř/ven
* Pokud stále dochází k problémům, otevřete lístek podpory s [podporou Microsoftu.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

