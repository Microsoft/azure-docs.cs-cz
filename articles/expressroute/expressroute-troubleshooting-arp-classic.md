---
title: 'Získávání tabulky ARP: Classic: řešení potíží s Azure ExpressRoute | Microsoft Docs'
description: Tato stránka obsahuje pokyny pro získání tabulky ARP pro okruh ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: carolz
editor: tysonn
ms.assetid: b5856acf-03c2-4933-8111-6ce12998d92a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: fcc847b7e30fd55ca759830e0254ab7542e7663e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850795"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Získání tabulky ARP v modelu nasazení classic
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede kroky pro získání tabulek protokolu ARP (Address Resolution Protocol) pro váš okruh Azure ExpressRoute.

> [!IMPORTANT]
> Tento dokument je určený vám pomohou diagnostikovat a opravit problémy, jednoduché. Rozhraní není určeno k jako náhrada za podporu společnosti Microsoft. Pokud problém nelze vyřešit pomocí následujících pokynů, otevřete žádost o podporu s [Microsoft Azure Nápověda a podpora](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresa tabulky Resolution Protocol (ARP) a protokolu ARP
ARP je protokol vrstvy 2, který je definován v [RFC 826](https://tools.ietf.org/html/rfc826). Slouží k mapování adresy Ethernet (adresy MAC) na IP adresu.

Základě tabulky ARP poskytuje mapování adresy IPv4 a adresa MAC pro konkrétní partnerský vztah. Tabulka protokolu ARP pro okruh ExpressRoute vztahy obsahuje následující informace pro každé rozhraní (primární i sekundární):

1. Mapování rozhraní směrovače místní IP adresy pro adresu MAC
2. Mapování rozhraní směrovače ExpressRoute IP adresy pro adresu MAC
3. Stáří mapování

Tabulky ARP může pomoct s Probíhá ověřování konfigurace vrstvy 2 a řešení potíží s problémy s připojením k základní vrstvy 2.

Tady je příklad tabulky ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Následující část obsahuje informace o tom, jak zobrazit tabulky ARP, které jsou vidět hraniční směrovače ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Předpoklady pro použití tabulky ARP
Ujistěte se, že máte následující, než budete pokračovat:

* Platný okruh ExpressRoute, který je nakonfigurovaný s alespoň jeden partnerský vztah. Okruh musí být plně nakonfigurované poskytovatelem připojení. Vy (nebo váš poskytovatel připojení) na musíte nakonfigurovat alespoň jeden z partnerských vztahů (Azure privátní, veřejný Azure nebo Microsoft) tento okruh.
* Rozsahy IP adres, které se používají ke konfiguraci partnerských vztahů (Azure privátní, veřejný Azure a Microsoft). Zkontrolujte příklady přiřazení IP adres v [stránky požadavky na směrování ExpressRoute](expressroute-routing.md) získat představu o tom, jak jsou IP adresy mapované na rozhraní na vaše aise a na straně pro ExpressRoute. Informace o konfiguraci partnerského vztahu můžete získat kontrolou [stránku konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-classic.md).
* Informace od poskytovatele tým nebo připojení k síťové adresy MAC rozhraní, které se používají s tyto IP adresy.
* Nejnovější modul Windows PowerShell pro Azure (verze 1.50 nebo novější).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabulky ARP pro váš okruh ExpressRoute
Tato část obsahuje informace o tom, jak zobrazit tabulky ARP pro každý typ partnerského vztahu pomocí prostředí PowerShell. Než budete pokračovat, vy nebo váš poskytovatel připojení je potřeba nakonfigurovat partnerský vztah. Každý okruh obsahuje dvě cesty (primární i sekundární). Tabulky ARP pro každou z cest můžete zkontrolovat nezávisle.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro soukromý partnerský vztah Azure
Následující rutiny najdete protokolu ARP tabulky pro soukromý partnerský vztah Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Toto je ukázkový výstup pro jeden z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabulky ARP pro veřejný partnerský vztah Azure:
Následující rutiny najdete protokolu ARP tabulky pro veřejný partnerský vztah Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Toto je ukázkový výstup pro jeden z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Toto je ukázkový výstup pro jeden z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabulky ARP pro partnerský vztah Microsoftu
Následující rutiny najdete protokolu ARP tabulky pro partnerský vztah Microsoftu:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Ukázkový výstup je zobrazena níže pro jednu z cesty:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak používat tyto informace
Tabulky ARP partnerský vztah můžete použít k ověření konfigurace vrstvy 2 a připojení. Tato část obsahuje přehled o tom, jak vzhledu tabulky ARP v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Na základě tabulky ARP při okruh je ve stavu provozní (očekávaný)
* Základě tabulky ARP má záznam pro místní straně s platnou adresu IP a MAC a podobně jako položka pro straně společnosti Microsoft.
* Poslední oktet místní IP adresa je vždy lichý počet.
* Poslední oktet Microsoft IP adresy je vždy sudé číslo.
* Zobrazí se stejnou adresu MAC na straně společnosti Microsoft pro všechny tři partnerské vztahy (primární nebo sekundární).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Na základě tabulky ARP, když je na místě nebo když straně připojení zprostředkovatele došlo k problémům
 V tabulce ARP se zobrazí pouze jedna položka. Zobrazuje mapování mezi adresu MAC a IP adresu, která se používá na straně společnosti Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Pokud dojde k problému jako to, otevřete žádost o podporu se svého poskytovatele připojení jeho řešení.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Na základě tabulky ARP při na straně společnosti Microsoft, má potíže s
* Neuvidíte na základě tabulky ARP pro partnerský vztah, pokud dochází k potížím na straně společnosti Microsoft.
* Otevřete žádost o podporu s [Microsoft Azure Nápověda a podpora](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Zadejte, že máte potíže s připojením vrstvy 2.

## <a name="next-steps"></a>Další kroky
* Ověření konfigurace vrstvy 3 pro váš okruh ExpressRoute:
  * Získá trasu souhrn k určení stavu relací protokolu BGP.
  * Získáte směrovací tabulku k určení, které předpony jsou ohlášené mezi ExpressRoute.
* Přenos dat ověřte kontrolou bajtů a odhlášení.
* Otevřete žádost o podporu s [Microsoft Azure Nápověda a podpora](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Pokud pořád dochází k problémům.

