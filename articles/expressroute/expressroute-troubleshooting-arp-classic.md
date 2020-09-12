---
title: 'Azure ExpressRoute: tabulky ARP – řešení potíží: klasický'
description: Tato stránka poskytuje pokyny pro získání tabulek ARP pro model nasazení Classic ExpressRoute okruhu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394835"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Získání tabulek protokolu ARP v modelu nasazení Classic
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede kroky pro získání tabulek protokolu ARP (Address Resolution Protocol) pro okruh Azure ExpressRoute.

> [!IMPORTANT]
> Tento dokument je určený k tomu, aby vám mohl pomoct diagnostikovat a opravovat jednoduché problémy. Není určena jako náhrada za podporu Microsoftu. Pokud problém nemůžete vyřešit pomocí následujících pokynů, otevřete žádost o podporu s [Microsoft Azure nápovědu a podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protokol ARP (Address Resolution Protocol) a ARP Tables
ARP je protokol vrstvy 2, který je definovaný v [dokumentu RFC 826](https://tools.ietf.org/html/rfc826). Protokol ARP slouží k mapování adresy Ethernet (adresy MAC) na IP adresu.

Tabulka ARP poskytuje mapování adresy IPv4 a adresy MAC pro konkrétní partnerský vztah. Tabulka ARP pro partnerský vztah okruhu ExpressRoute poskytuje pro každé rozhraní (primární a sekundární) následující informace:

1. Mapování IP adresy místního rozhraní směrovače na adresu MAC
2. Mapování IP adresy rozhraní směrovače ExpressRoute na adresu MAC
3. Stáří mapování

Tabulky ARP můžou pomáhat s ověřováním konfigurace vrstvy 2 a řešením potíží se základní vrstvou s připojením vrstvy 2.

Následuje příklad tabulky protokolu ARP:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


V následující části najdete informace o tom, jak zobrazit tabulky ARP, které vidí směrovače ExpressRoute Edge.

## <a name="prerequisites-for-using-arp-tables"></a>Předpoklady použití tabulek ARP
Než budete pokračovat, ujistěte se, že máte následující:

* Platný okruh ExpressRoute, který je nakonfigurovaný aspoň s jedním partnerským vztahem. Okruh musí být plně nakonfigurovaný poskytovatelem připojení. V tomto okruhu musíte (nebo poskytovatel připojení) nakonfigurovat aspoň jedno z partnerských vztahů (privátní Azure, veřejný nebo Microsoft).
* Rozsahy IP adres, které se používají ke konfiguraci partnerských vztahů (privátní Azure, veřejný Azure a Microsoft). Podívejte se na příklady přiřazení IP adres na [stránce požadavky na směrování ExpressRoute](expressroute-routing.md) , kde se seznámíte s tím, jak se IP adresy mapují na rozhraní na vaší straně a na ExpressRoute straně. Informace o konfiguraci partnerského vztahu můžete získat pomocí [stránky konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-classic.md).
* Informace z vašeho síťového týmu nebo poskytovatele připojení o adresách MAC rozhraní, která se používají s těmito IP adresami.
* Nejnovější modul Windows PowerShellu pro Azure (verze 1,50 nebo novější).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabulky ARP pro okruh ExpressRoute
V této části najdete informace o tom, jak zobrazit tabulky ARP pro každý typ partnerského vztahu pomocí prostředí PowerShell. Než budete pokračovat, budete vy nebo váš poskytovatel připojení potřebovat nakonfigurovat partnerský vztah. Každý okruh má dvě cesty (primární a sekundární). Tabulku ARP můžete pro každou cestu kontrolovat nezávisle.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro privátní partnerské vztahy Azure
Následující rutina poskytuje tabulky ARP pro privátní partnerské vztahy Azure:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

Následuje ukázkový výstup pro jednu z cest:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabulky ARP pro veřejné partnerské vztahy Azure:
Následující rutina poskytuje tabulky ARP pro veřejné partnerské vztahy Azure:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

Následuje ukázkový výstup pro jednu z cest:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Následuje ukázkový výstup pro jednu z cest:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabulky ARP pro partnerský vztah Microsoftu
Následující rutina poskytuje tabulky ARP pro partnerský vztah Microsoftu:

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


Vzorový výstup se zobrazuje níže pro jednu z cest:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Jak používat tyto informace
Tabulku ARP partnerského vztahu lze použít k ověření konfigurace a připojení vrstvy 2. Tato část poskytuje přehled o tom, jak tabulky ARP vypadají v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabulka ARP, když je okruh v provozním (očekávaném) stavu
* Tabulka ARP obsahuje položku pro místní stranu s platnou IP adresou a adresou MAC a podobnou položkou pro stranu Microsoftu.
* Poslední oktet místní IP adresy je vždycky liché číslo.
* Poslední oktet IP adresy Microsoftu je vždycky sudé číslo.
* Na straně Microsoftu se zobrazí stejná adresa MAC pro všechny tři partnerské vztahy (primární/sekundární).

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabulka ARP, když je místní nebo když má strana poskytovatele připojení problémy
 V tabulce ARP se zobrazí pouze jedna položka. Zobrazuje mapování mezi adresou MAC a IP adresou, která se používá na straně Microsoftu.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> Pokud dojde k problému, otevřete žádost o podporu u poskytovatele připojení a vyřešte ho.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabulka protokolu ARP, když má strana Microsoftu problémy
* V případě, že se na straně Microsoftu vyskytují problémy, nezobrazí se tabulka protokolu ARP zobrazená pro partnerský vztah.
* Otevřete žádost o podporu pomocí [Microsoft Azure nápovědě a podpoře](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Určete, že máte problém s připojením vrstvy 2.

## <a name="next-steps"></a>Další kroky
* Ověřit konfigurace vrstvy 3 pro okruh ExpressRoute:
  * Získejte souhrn trasy, který určí stav relací protokolu BGP.
  * Získejte směrovací tabulku, abyste zjistili, které předpony se budou inzerovat napříč ExpressRoute.
* Ověření přenosu dat kontrolou bajtů.
* Pokud stále dochází k problémům, otevřete žádost o podporu s [Microsoft Azure nápovědě a podpoře](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

