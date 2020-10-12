---
title: 'Azure ExpressRoute: tabulky ARP – řešení potíží'
description: Tato stránka poskytuje pokyny k získání tabulek ARP pro okruh ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 9272bb8bac2054d7a02a7eac8c214395a86ceebf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394852"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Získání tabulek protokolu ARP v modelu nasazení Správce prostředků
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Classic](expressroute-troubleshooting-arp-classic.md)
> 
> 

Tento článek vás provede jednotlivými kroky pro seznámení s tabulkami ARP pro okruh ExpressRoute.

> [!IMPORTANT]
> Tento dokument je určený k tomu, aby vám mohl pomoct diagnostikovat a opravovat jednoduché problémy. Není určena jako náhrada za podporu Microsoftu. Pokud tento problém nemůžete vyřešit pomocí pokynů uvedených níže, musíte otevřít lístek podpory s [podporou Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protokol ARP (Address Resolution Protocol) a ARP Tables
Protokol ARP (Address Resolution Protocol) je protokol vrstvy 2 definovaný v [dokumentu RFC 826](https://tools.ietf.org/html/rfc826). Protokol ARP slouží k mapování adresy Ethernet (adresy MAC) s IP adresou.

Tabulka ARP poskytuje mapování adresy IPv4 a adresy MAC pro konkrétní partnerský vztah. Tabulka ARP pro partnerský vztah okruhu ExpressRoute poskytuje následující informace pro každé rozhraní (primární a sekundární).

1. Mapování IP adresy místního rozhraní směrovače na adresu MAC
2. Mapování IP adresy rozhraní směrovače ExpressRoute na adresu MAC
3. Stáří mapování

Tabulky ARP můžou pomáhat ověřit konfiguraci vrstvy 2 a řešit problémy s připojením základní vrstvy 2. 

Příklad tabulky protokolu ARP: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


V následující části najdete informace o tom, jak můžete zobrazit tabulky ARP zobrazené směrovači ExpressRoute Edge. 

## <a name="prerequisites-for-learning-arp-tables"></a>Předpoklady pro získávání tabulek ARP
Než budete pokračovat, ujistěte se, že máte následující:

* Platný okruh ExpressRoute nakonfigurovaný minimálně s jedním partnerským vztahem. Okruh musí být plně nakonfigurovaný poskytovatelem připojení. U tohoto okruhu musíte (nebo poskytovatel připojení) nakonfigurovat aspoň jedno z partnerských vztahů (privátní Azure, Azure Public a Microsoft).
* Rozsahy IP adres, které se používají ke konfiguraci partnerských vztahů (privátní Azure, veřejný Azure a Microsoft). Podívejte se na příklady přiřazení IP adres na [stránce požadavky na směrování ExpressRoute](expressroute-routing.md) , kde se seznámíte s tím, jak se IP adresy mapují na rozhraní na vaší straně a na ExpressRoute straně. Informace o konfiguraci partnerského vztahu můžete získat pomocí [stránky konfigurace partnerského vztahu ExpressRoute](expressroute-howto-routing-arm.md).
* Informace z vašeho síťového týmu/poskytovatele připojení na adresách MAC rozhraní používaných s těmito IP adresami.
* Musíte mít nejnovější modul PowerShellu pro Azure (verze 1,50 nebo novější).

> [!NOTE]
> Pokud poskytovatel služby poskytuje vrstvu 3 a tabulky ARP jsou v níže uvedeném portálu/výstupu prázdné, aktualizujte konfiguraci okruhu pomocí tlačítka Aktualizovat na portálu. Tato operace použije na okruhu správnou konfiguraci směrování. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Získávání tabulek ARP pro okruh ExpressRoute
V této části najdete informace o tom, jak můžete zobrazit tabulky ARP na partnerské vztahy pomocí prostředí PowerShell. Než budete pokračovat, musíte vy nebo váš poskytovatel připojení nakonfigurovat partnerský vztah. Každý okruh má dvě cesty (primární a sekundární). Tabulku ARP můžete pro každou cestu kontrolovat nezávisle.

### <a name="arp-tables-for-azure-private-peering"></a>Tabulky ARP pro privátní partnerské vztahy Azure
Následující rutina poskytuje tabulky ARP pro privátní partnerské vztahy Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

Vzorový výstup se zobrazuje níže pro jednu z cest.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabulky ARP pro veřejné partnerské vztahy Azure
Následující rutina poskytuje tabulky ARP pro veřejné partnerské vztahy Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


Vzorový výstup se zobrazuje níže pro jednu z cest.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabulky ARP pro partnerský vztah Microsoftu
Následující rutina poskytuje tabulky ARP pro partnerský vztah Microsoftu.

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


Vzorový výstup se zobrazuje níže pro jednu z cest.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Jak používat tyto informace
Tabulku ARP partnerského vztahu lze použít k určení ověřování konfigurace a připojení vrstvy 2. Tato část poskytuje přehled o tom, jak budou tabulky ARP vypadat v různých scénářích.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabulka ARP, když je okruh v provozním stavu (očekávaný stav)
* Tabulka ARP bude obsahovat položku pro místní stranu s platnou IP adresou a adresou MAC a podobnou položkou pro stranu Microsoftu. 
* Poslední oktet místní IP adresy bude vždycky liché číslo.
* Poslední oktet IP adresy Microsoftu bude vždycky sudé číslo.
* Na straně Microsoftu se zobrazí stejná adresa MAC pro všechny 3 partnerské vztahy (primární/sekundární). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabulka protokolu ARP, pokud je na straně místního nebo poskytovatele připojení problémy
Pokud se vyskytnou problémy s místním připojením nebo poskytovatelem připojení, může se stát, že se v tabulce ARP zobrazí jenom jedna položka, nebo se na místní adrese MAC zobrazí možnost nekompletní. Tím se zobrazí mapování mezi adresou MAC a IP adresou použitou na straně Microsoftu. 
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

nebo
       
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


> [!NOTE]
> Pokud chcete ladit takové problémy, otevřete žádost o podporu u svého poskytovatele připojení. Pokud tabulka protokolu ARP nemá IP adresy rozhraní namapovaných na adresy MAC, přečtěte si následující informace:
> 
> 1. Pokud se první IP adresa podsítě/30 přiřazené k propojení mezi MSEE-PR a MSEE používá v rozhraní MSEE-PR. Azure vždycky používá druhou IP adresu pro směrovači msee.
> 2. Ověřte, zda značky VLAN zákazníka (značka C) a Service (S-tag) odpovídají obou dvojicím MSEE-PR a MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabulka protokolu ARP, když má strana Microsoftu problémy
* V případě, že se na straně Microsoftu vyskytují problémy, nezobrazí se tabulka protokolu ARP zobrazená pro partnerský vztah. 
* Otevřete lístek podpory s [podporou Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Určete, že máte problém s připojením vrstvy 2. 

## <a name="next-steps"></a>Další kroky
* Ověření konfigurace vrstvy 3 pro okruh ExpressRoute
  * Získat souhrn tras k určení stavu relací protokolu BGP 
  * Získat směrovací tabulku, která určuje, které předpony se budou inzerovat napříč ExpressRoute
* Ověření přenosu dat kontrolou bajtů v/v
* Pokud stále dochází k problémům, otevřete lístek podpory s [podporou Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

