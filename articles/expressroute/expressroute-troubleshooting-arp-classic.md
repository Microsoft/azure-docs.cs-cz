---
title: 'Azure ExpressRoute: Tabulky ARP – řešení potíží: klasické'
description: Tato stránka obsahuje pokyny pro získání tabulek ARP pro okruh ExpressRoute - klasický model nasazení.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: 7c223e3802d499e002b12580b17cb9ee3f1bea97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076617"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Získání tabulek ARP v klasickém modelu nasazení
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede kroky pro získání tabulek protokolu ARP (Address Resolution Protocol) pro okruh Azure ExpressRoute.

> [!IMPORTANT]
> Tento dokument je určen k diagnostice a opravě jednoduchých problémů. Není určena jako náhrada za podporu společnosti Microsoft. Pokud problém nemůžete vyřešit pomocí následujících pokynů, otevřete žádost o podporu s [nápovědou k Podpoře Microsoft Azure .](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabulky protokolu ARP (Address Resolution Protocol) a ARP
ARP je protokol vrstvy 2, který je definován v [rfc 826](https://tools.ietf.org/html/rfc826). Protokol ARP slouží k mapování ethernetové adresy (MAC adresy) na IP adresu.

Tabulka ARP obsahuje mapování adresy IPv4 a adresy MAC pro konkrétní partnerský vztah. Tabulka ARP pro partnerský vztah okruhu ExpressRoute poskytuje následující informace pro každé rozhraní (primární a sekundární):

1. Mapování adresy IP rozhraní místního směrovače na adresu MAC
2. Mapování IP adresy rozhraní směrovače ExpressRoute na adresu MAC
3. Stáří mapování

Tabulky ARP mohou pomoci s ověřováním konfigurace vrstvy 2 a s řešením problémů se základním připojením vrstvy 2.

Následuje příklad tabulky ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Následující část obsahuje informace o tom, jak zobrazit tabulky ARP, které jsou vidět směrovače expressroute okraj.

## <a name="prerequisites-for-using-arp-tables"></a>Předpoklady pro použití tabulek ARP
Než budete pokračovat, ujistěte se, že máte následující:

* Platný okruh ExpressRoute, který je nakonfigurován s alespoň jedním partnerským vztahem. Okruh musí být plně nakonfigurován poskytovatelem připojení. Vy (nebo váš poskytovatel připojení) musíte nakonfigurovat alespoň jeden z partnerských styků (Azure private, Azure public nebo Microsoft) v tomto okruhu.
* Rozsahy IP adres, které se používají ke konfiguraci partnerských uživatelů (Azure private, Azure public a Microsoft). Projděte si příklady přiřazení IP adres na [stránce požadavky na směrování ExpressRoute,](expressroute-routing.md) abyste získali představu o tom, jak jsou IP adresy mapovány na rozhraní na vaší straně a na straně ExpressRoute. Informace o konfiguraci partnerského vztahu můžete získat kontrolou [stránky konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-classic.md).
* Informace od síťového týmu nebo poskytovatele připojení o mac adresách rozhraní, které se používají s těmito ADRESAMI IP.
* Nejnovější modul Windows PowerShell pro Azure (verze 1.50 nebo novější).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabulky ARP pro okruh ExpressRoute
Tato část obsahuje pokyny, jak zobrazit tabulky ARP pro každý typ partnerského vztahu pomocí prostředí PowerShell. Než budete pokračovat, vy nebo váš poskytovatel připojení musí nakonfigurovat partnerský vztah. Každý okruh má dvě cesty (primární a sekundární). Tabulku ARP můžete zkontrolovat pro každou cestu nezávisle.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro privátní partnerský vztah Azure
Následující rutina poskytuje tabulky ARP pro privátní partnerský vztah Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Následuje ukázkový výstup pro jednu z cest:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabulky ARP pro veřejný partnerský vztah Azure:
Následující rutina poskytuje tabulky ARP pro veřejný partnerský vztah Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Následuje ukázkový výstup pro jednu z cest:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Následuje ukázkový výstup pro jednu z cest:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabulky ARP pro partnerský vztah Microsoftu
Následující rutina obsahuje tabulky ARP pro partnerský vztah Microsoftu:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Ukázkový výstup je uveden níže pro jednu z cest:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Jak tyto informace používat
Tabulku ARP partnerského vztahu lze použít k ověření konfigurace vrstvy 2 a připojení. Tato část obsahuje přehled o tom, jak tabulky ARP vypadají v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP tabulka, pokud je obvod v provozním (očekávaném) stavu
* Tabulka ARP obsahuje položku pro místní stranu s platnou adresou IP a MAC a podobnou položkou na straně Microsoft.
* Poslední oktet místní IP adresy je vždy liché číslo.
* Poslední oktet adresy MICROSOFT IP je vždy sudé číslo.
* Stejná adresa MAC se zobrazí na straně Microsoftu pro všechny tři partnerské vztahy (primární/sekundární).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP tabulka, když je místní nebo když má problémy na straně poskytovatele připojení
 V tabulce ARP se zobrazí pouze jedna položka. Zobrazuje mapování mezi adresou MAC a IP adresou, která se používá na straně společnosti Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Pokud narazíte na problém, jako je tento, otevřete žádost o podporu s poskytovatelem připojení k jeho vyřešení.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP tabulka, pokud na straně Microsoft u sebe mají problémy
* Pokud se na straně microsoftu objeví tabulka ARP pro partnerský vztah, nezobrazí se tabulka ARP.
* Otevřete žádost o podporu pomocí [nápovědy k podpoře Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Určete, že máte problém s připojením vrstvy 2.

## <a name="next-steps"></a>Další kroky
* Ověřte konfigurace vrstvy 3 pro okruh ExpressRoute:
  * Získejte souhrn trasy k určení stavu relací Protokolu BGP.
  * Získejte směrovací tabulku, která určí, které předpony jsou inzerovány v rámci ExpressRoute.
* Ověřte přenos dat kontrolou bajtů dovnitř a ven.
* Pokud máte stále problémy, otevřete žádost o podporu pomocí [nápovědy+podpory Microsoft Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

