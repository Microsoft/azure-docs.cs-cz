---
title: 'Získání tabulek protokolu ARP - řešení potíží – ExpressRoute: Azure | Dokumentace Microsoftu'
description: Tahle stránka poskytuje pokyny na získání tabulek ARP pro okruh ExpressRoute
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 1807bda35f6bfcc9dbbb30f054cedb9454a88a7f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158566"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Získání tabulek protokolu ARP v modelu nasazení Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede kroky další tabulky ARP pro váš okruh ExpressRoute.

> [!IMPORTANT]
> Účelem tohoto dokumentu je vám pomohou diagnostikovat a opravovat problémy jednoduché. Není určen jako náhrada za podporu Microsoftu. Je nutné otevřít lístek podpory s [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Pokud nemůžete vyřešit problém s využitím pokynů je popsáno níže.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresa tabulky Resolution Protocol (ARP) a protokolu ARP
Protokol ARP (Address Resolution) je protokol vrstvy 2 definované v [RFC 826](https://tools.ietf.org/html/rfc826). Slouží k mapování sítě Ethernet (adresy MAC) s ip adresou.

Základě tabulky ARP poskytuje mapování ipv4 adresa a adresa MAC pro konkrétní partnerský vztah. Tabulky ARP pro okruh ExpressRoute peering obsahuje následující informace pro každé rozhraní (primární i sekundární)

1. Mapování místní směrovač rozhraní ip adresu na adresu MAC.
2. Mapování ExpressRoute směrovače rozhraní ip adresu na adresu MAC.
3. Stáří mapování

Tabulek protokolu ARP může pomoci ověřit konfiguraci vrstvy 2 a řešení potíží s základní vrstvy 2 problémy s připojením. 

Příklad tabulky ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


Následující část obsahuje informace o zobrazení tabulek protokolu ARP vidět hraniční směrovače pro ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Požadavky pro výuku tabulek protokolu ARP
Zkontrolujte, že máte následující před další průběhu

* Okruh ExpressRoute platný nakonfigurovanou aspoň jeden partnerský vztah. Okruh musí být plně nakonfigurované poskytovatelem připojení. Vy (nebo váš poskytovatel připojení) musíte nakonfigurovat aspoň jeden z partnerské vztahy (Azure privátní, veřejný Azure a Microsoft) na tomto okruhu.
* Rozsahy IP adres používá ke konfiguraci partnerských vztahů (Azure privátní, veřejný Azure a Microsoft). Projděte si příklady přiřazení ip adres v [stránce požadavky směrování ExpressRoute](expressroute-routing.md) k pomůžou pochopit, jak jsou ip adresy mapované na rozhraní na vaší straně a na straně ExpressRoute. Informace o konfiguraci partnerského vztahu můžete získat kontrolou [stránka konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-arm.md).
* Informace z síťovým týmem / připojení k poskytovateli na MAC adresy rozhraní používají se tyto IP adresy.
* Musí mít nejnovější modul Powershellu pro Azure (verze 1.50 nebo novější).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Získání tabulek ARP pro váš okruh ExpressRoute
Tato část obsahuje informace o zobrazení tabulek protokolu ARP každý partnerský vztah, pomocí Powershellu. Vy nebo váš poskytovatel připojení musíte nakonfigurovat partnerský vztah před postupujte dále. Každý okruh má dvě cesty (primární i sekundární). Můžete zkontrolovat základě tabulky ARP pro každou cestu nezávisle na sobě.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro soukromý partnerský vztah Azure
Následující rutiny najdete protokolu ARP tabulky pro soukromý partnerský vztah Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Ukázkový výstup najdete níž pro jeden z cesty

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

        # ARP table for Azure public peering - Secondary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Ukázkový výstup najdete níž pro jeden z cesty

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabulek protokolu ARP pro partnerský vztah Microsoftu
Následující rutiny najdete protokolu ARP tabulky pro partnerské vztahy Microsoftu

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Ukázkový výstup najdete níž pro jeden z cesty

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak používat tyto informace
Základě tabulky ARP partnerského slouží k určení ověření vrstvy 2 konfigurací a připojením. Tato část obsahuje základní informace o tom, jak bude vypadat tabulek protokolu ARP v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabulky protokolu ARP, když je okruh ve provozní stav (očekávaný stav)
* Základě tabulky ARP bude mít položka pro stranu místní s platnou IP adresu a adresu MAC a podobně jako položka na straně Microsoftu. 
* Poslední oktet místní ip adresa bude vždy liché číslo.
* Poslední oktet ip adresy, Microsoft bude vždy sudé číslo.
* Danou adresu MAC se zobrazí na straně Microsoftu pro všechny 3 partnerské vztahy (primární nebo sekundární). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Kdy tabulky protokolu ARP v místním nebo připojení zprostředkovatele na straně má problémy
Pokud dojde k problémům v místním nebo poskytovatele připojení, které může vidět, že buď pouze jedna položka se zobrazí v tabulce ARP nebo adresa MAC v místním prostředí se zobrazí neúplné. Tím se zobrazí mapování mezi adresu MAC a IP adresu použitou na straně Microsoftu. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

nebo
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Žádost o podporu u svého poskytovatele připojení, chcete-li ladit tyto problémy. Pokud základě tabulky ARP nemá žádné IP adresy rozhraní namapované na adresy MAC, projděte si následující informace:
> 
> 1. Pokud první IP adresa/30 podsítě přiřazené pro propojení mezi směrovači MSEE – žádost o přijetí změn a směrovači MSEE se používá v rozhraní směrovači MSEE – žádosti o přijetí změn Azure vždy používá druhou IP adresu pro Msee.
> 2. Ověřte, pokud zákazník (C-Tag) a sítě VLAN značky služeb (S-Tag) odpovídají na pár směrovači MSEE – žádost o přijetí změn a směrovači MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabulky protokolu ARP, když má problémy na straně Microsoftu
* Neuvidíte základě tabulky ARP pro partnerský vztah, pokud existují problémy na straně Microsoftu. 
* Vytvořit lístek podpory s [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Zadejte, že máte potíže s připojení vrstvy 2. 

## <a name="next-steps"></a>Další kroky
* Ověření vrstvy 3 konfigurace pro váš okruh ExpressRoute
  * Získejte přehled k určení stavu relace protokolu BGP trasy 
  * Získejte směrovací tabulku k určení, které předpony jsou ohlašovaných přes ExpressRoute
* Ověřit kontrolou bajty příchozí / odchozí přenos dat
* Vytvořit lístek podpory s [podpory Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Pokud stále dochází k problémům.

