---
title: 'Získejte řešení potíží s ExpressRoute tabulky - ARP: classic: Azure | Dokumentace Microsoftu'
description: Tahle stránka poskytuje pokyny pro získání tabulek protokolu ARP pro okruh ExpressRoute – model nasazení classic.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 367a79b04a8736e2eafb6851b682f2c244e80522
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272282"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Získání tabulek protokolu ARP v modelu nasazení classic
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede kroky pro získání tabulek protokolu ARP (Address Resolution) pro váš okruh Azure ExpressRoute.

> [!IMPORTANT]
> Účelem tohoto dokumentu je vám pomohou diagnostikovat a opravovat problémy jednoduché. Není určen jako náhrada za podporu Microsoftu. Pokud problém nelze vyřešit pomocí následujících pokynů, otevřete žádost o podporu s [Microsoft Azure Nápověda a podpora](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresa tabulky Resolution Protocol (ARP) a protokolu ARP
ARP je protokol vrstvy 2, který je definován v [RFC 826](https://tools.ietf.org/html/rfc826). Slouží k mapování adresy Ethernet (adresa MAC) na IP adresu.

Základě tabulky ARP poskytuje mapování IPv4 adresa a adresa MAC pro konkrétní partnerský vztah. Tabulky ARP pro okruh ExpressRoute peering obsahuje následující informace pro každé rozhraní (primární i sekundární):

1. Mapování rozhraní směrovače místní IP adresu na adresu MAC
2. Mapování adresy ExpressRoute směrovače rozhraní IP adrese MAC.
3. Stáří mapování

Tabulek protokolu ARP může pomoct s ověření vrstvy 2 konfigurace a řešení potíží s problémy s připojením základní vrstvy 2.

Následuje příklad tabulky ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Následující část obsahuje informace o tom, jak zobrazit tabulek protokolu ARP, které jsou vidět hraniční směrovače pro ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Předpoklady pro použití tabulek protokolu ARP
Ujistěte se, že máte následující předtím, než budete pokračovat:

* Platný okruh ExpressRoute, který je nakonfigurovaný pomocí nejméně jeden partnerský vztah. Okruh musí být plně nakonfigurované poskytovatelem připojení. Vy (nebo váš poskytovatel připojení) musíte splnit aspoň jednu z partnerské vztahy (Azure privátní, veřejný Azure nebo Microsoft) nakonfigurovat na tento okruh.
* Rozsahy IP adres, které se používají ke konfiguraci partnerských vztahů (Azure privátní, veřejný Azure a Microsoft). Projděte si příklady přiřazení IP adres v [stránce požadavky směrování ExpressRoute](expressroute-routing.md) k pomůžou pochopit, jak jsou IP adresy mapované na rozhraní na vaše aise a na straně ExpressRoute. Informace o konfiguraci partnerského vztahu můžete získat kontrolou [stránka konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-classic.md).
* Informace od poskytovatele síťových týmu nebo připojení o adresách MAC rozhraní, které se používají se tyto IP adresy.
* Nejnovější modul prostředí Windows PowerShell pro Azure (verze 1.50 nebo novější).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabulky ARP pro váš okruh ExpressRoute
Tato část obsahuje informace o tom, jak zobrazit tabulky ARP pro každý typ partnerského vztahu s použitím prostředí PowerShell. Než budete pokračovat, buď vy nebo váš poskytovatel připojení je potřeba nakonfigurovat partnerský vztah. Každý okruh má dvě cesty (primární i sekundární). Můžete zkontrolovat základě tabulky ARP pro každou cestu nezávisle na sobě.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro soukromý partnerský vztah Azure
Tuto rutinu najdete tabulky ARP pro soukromý partnerský vztah Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Tady je ukázkový výstup pro jeden z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabulky ARP pro veřejný partnerský vztah Azure:
Tuto rutinu najdete tabulky ARP pro veřejný partnerský vztah Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Tady je ukázkový výstup pro jeden z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Tady je ukázkový výstup pro jeden z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabulek protokolu ARP pro partnerský vztah Microsoftu
Tuto rutinu najdete tabulky ARP pro partnerský vztah Microsoftu:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Ukázkový výstup najdete níž pro jeden z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak používat tyto informace
Základě tabulky ARP partnerského slouží k ověření vrstvy 2 konfigurací a připojením. Tato část obsahuje základní informace o vzhled tabulek protokolu ARP v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabulky protokolu ARP, když okruh je ve stavu provozní (očekávaný)
* Základě tabulky ARP má záznam pro místní straně s platnou adresu IP a MAC a podobně jako položka na straně Microsoftu.
* Poslední oktet místní IP adresa je vždy liché číslo.
* Poslední oktet Microsoft IP adresa je vždy sudé číslo.
* Danou adresu MAC se zobrazí na straně Microsoftu pro všechny tři partnerské vztahy (primární nebo sekundární).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabulky protokolu ARP, když je v místním nebo pokud poskytovatel připojení na straně má problémy
 V tabulce ARP se zobrazí pouze jedna položka. Zobrazuje mapování mezi adresu MAC a IP adresu, která se používá na straně Microsoftu.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Pokud dochází k problému tímto způsobem, otevřete žádost o podporu u svého poskytovatele připojení k jeho vyřešení.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabulky protokolu ARP, když má problémy na straně Microsoftu
* Neuvidíte základě tabulky ARP pro partnerský vztah, pokud existují problémy na straně Microsoftu.
* Žádost o podporu s [Microsoft Azure Nápověda a podpora](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Zadejte, že máte potíže s připojení vrstvy 2.

## <a name="next-steps"></a>Další postup
* Ověření vrstvy 3 konfigurace pro váš okruh ExpressRoute:
  * Získejte přehled k určení stavu relace protokolu BGP trasy.
  * Získejte směrovací tabulku k určení, které předpony jsou ohlašovaných přes ExpressRoute.
* Ověření přenosu dat dovnitř a ven kontrolou bajtů.
* Žádost o podporu s [Microsoft Azure Nápověda a podpora](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Pokud stále dochází k problémům.

