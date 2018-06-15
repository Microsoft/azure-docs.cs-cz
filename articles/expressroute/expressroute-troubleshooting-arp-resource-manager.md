---
title: 'Získávání tabulky ARP: Resource Manager: řešení potíží s Azure ExpressRoute | Microsoft Docs'
description: Tato stránka obsahuje pokyny, získávání tabulky ARP pro okruh ExpressRoute
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: 0a6bf1d5-6baf-44dd-87d3-1ebd2fd08bdc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: a65b1ba2998eae33b3e73bd2492fbbf025eb5946
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850781"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Získání tabulky ARP v modelu nasazení Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede kroky pro další tabulky ARP pro váš okruh ExpressRoute. 

> [!IMPORTANT]
> Tento dokument je určený vám pomohou diagnostikovat a opravit problémy, jednoduché. Rozhraní není určeno k jako náhrada za podporu společnosti Microsoft. Je nutné otevřít lístek podpory s [podporu společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) nejde k jejich vyřešení podle pokynů popsaných níže.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresa tabulky Resolution Protocol (ARP) a protokolu ARP
Protokol ARP (Address Resolution Protocol) je protokol vrstvy 2 definované v [RFC 826](https://tools.ietf.org/html/rfc826). Slouží k mapování adresa sítě Ethernet (adresy MAC) s ip adresou.

Základě tabulky ARP poskytuje mapování adresy ipv4 a adresa MAC pro konkrétní partnerský vztah. Tabulky ARP pro okruh ExpressRoute vztahy poskytuje následující informace pro každé rozhraní (primární i sekundární)

1. Mapování místní směrovač rozhraní ip adresy pro adresu MAC
2. Mapování ExpressRoute směrovač rozhraní ip adresy pro adresu MAC
3. Stáří mapování

Tabulky ARP může pomoci ověřit konfiguraci vrstvy 2 a řešení potíží s základní vrstvy 2 problémy s připojením. 

Příklad tabulky ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Následující část obsahuje informace o tom, jak můžete zobrazit tabulky ARP pohledu hraniční směrovače ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Předpoklady pro učení tabulky ARP
Zajistěte, abyste měli následující předtím, než jste další průběhu

* Okruh ExpressRoute platný nakonfigurované alespoň jeden partnerský vztah. Okruh musí být plně nakonfigurované poskytovatelem připojení. Vy (nebo váš poskytovatel připojení) musí mít nakonfigurované alespoň jeden z partnerských vztahů (Azure privátní, veřejný Azure a Microsoft) na tomto okruhu.
* Rozsahy IP adres použitý ke konfiguraci partnerských vztahů (Azure privátní, veřejný Azure a Microsoft). Zkontrolujte příklady přiřazení ip adres v [stránky požadavky na směrování ExpressRoute](expressroute-routing.md) získat představu o tom, jak jsou ip adresy mapované na rozhraní na vaší straně a na straně ExpressRoute. Informace o konfiguraci partnerského vztahu můžete získat kontrolou [stránku konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-arm.md).
* Informace od týmu pro sítě / poskytovatele připojení na adresy MAC rozhraní použít s tyto IP adresy.
* Musí mít nejnovější modul prostředí PowerShell pro Azure (verze 1.50 nebo novější).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Získávání tabulky ARP pro váš okruh ExpressRoute
Tato část obsahuje informace o tom, jak můžete zobrazit tabulky ARP na partnerský vztah pomocí prostředí PowerShell. Vy nebo váš poskytovatel připojení musíte nakonfigurovat partnerský vztah než pokročíte Další. Každý okruh obsahuje dvě cesty (primární i sekundární). Tabulky ARP pro každou z cest můžete zkontrolovat nezávisle.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro soukromý partnerský vztah Azure
Následující rutiny najdete protokolu ARP tabulky pro soukromý partnerský vztah Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Ukázkový výstup je zobrazena níže pro jedna z cest

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabulky ARP pro veřejný partnerský vztah Azure
Následující rutiny najdete protokolu ARP tabulky pro veřejný partnerský vztah Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Ukázkový výstup je zobrazena níže pro jedna z cest

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabulky ARP pro partnerský vztah Microsoftu
Následující rutiny najdete protokolu ARP tabulky pro partnerský vztah Microsoftu

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Ukázkový výstup je zobrazena níže pro jedna z cest

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak používat tyto informace
Tabulky ARP partnerský vztah můžete použít k určení ověřit konfiguraci vrstvy 2 a připojení. Tato část obsahuje přehled o tom, jak bude vypadat tabulky ARP v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Na základě tabulky ARP když okruh v provozní stav (očekávaný stav)
* Tabulky ARP bude mít položka pro místní straně s platnou IP adresu a adresu MAC a podobně jako položka pro straně společnosti Microsoft. 
* Poslední oktet místní ip adresu, bude vždy lichý počet.
* Poslední oktet ip adresy Microsoft bude vždy sudé číslo.
* Zobrazí se stejnou adresu MAC na straně společnosti Microsoft pro všechny 3 partnerské vztahy (primární / sekundární). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabulky ARP, kdy místní / straně připojení zprostředkovatele došlo k problémům
Pokud dochází k potížím s místní nebo poskytovatele připojení, které se můžete setkat, zobrazí se buď jenom jedna položka v tabulky ARP nebo adresu MAC místní zobrazí neúplné. Zobrazí mapování mezi adresa MAC a IP adresu použitou na straně společnosti Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

nebo
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Otevřete žádost o podporu se svého poskytovatele připojení k ladění tyto problémy. Pokud základě tabulky ARP nemá IP adresy rozhraní namapované na adresy MAC, projděte si následující informace:
> 
> 1. Pokud první IP adresa/30 podsítě přiřazené pro propojení mezi MSEE PR a MSEE se používá na rozhraní MSEE PR. Azure vždy používá druhou IP adresu pro Msee.
> 2. Ověřte, pokud zákazník (C-Tag) a značky VLAN služby (S-Tag) odpovídají na dvojice MSEE PR a MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Na základě tabulky ARP při Microsoft straně má potíže s
* Neuvidíte na základě tabulky ARP pro partnerský vztah, pokud dochází k potížím na straně společnosti Microsoft. 
* Otevřete lístek podpory s [podporu společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Zadejte, že máte potíže s připojením vrstvy 2. 

## <a name="next-steps"></a>Další kroky
* Ověření konfigurace vrstvy 3 pro váš okruh ExpressRoute
  * Získání trasy shrnutí k určení stavu relací protokolu BGP 
  * Získejte tabulku směrování k určení, které předpony jsou ohlášené mezi ExpressRoute
* Ověřit kontrolou bajtů vstupně -výstupnímu přenos dat
* Otevřete lístek podpory s [podporu společnosti Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Pokud pořád dochází k problémům.

