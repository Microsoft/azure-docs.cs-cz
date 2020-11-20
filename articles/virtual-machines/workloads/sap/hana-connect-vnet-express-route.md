---
title: Nastavení připojení z virtuální sítě na SAP HANA v Azure (velké instance) | Microsoft Docs
description: Nastavení připojení z virtuální sítě pro použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c5a8a8157721f34abf7761a85febc7bcea3abb88
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967835"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Připojení virtuální sítě k velkým instancím HANA

Po vytvoření virtuální sítě Azure se tato síť dá připojit k SAP HANA ve velkých instancích Azure. Vytvořte ve virtuální síti bránu Azure ExpressRoute. Tato brána umožňuje propojit virtuální síť k okruhu ExpressRoute, který se připojuje k tenantovi zákazníka na velké instanci razítka HANA.

> [!NOTE] 
> Dokončení tohoto kroku může trvat až 30 minut. Nová brána se vytvoří v určeném předplatném Azure a pak se připojí k zadané službě Azure Virtual Network.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Pokud brána již existuje, ověřte, zda se jedná o bránu ExpressRoute. Pokud se nejedná o bránu ExpressRoute, odstraňte bránu a znovu ji vytvořte jako bránu ExpressRoute. Pokud je už brána ExpressRoute vytvořená, přečtěte si následující část tohoto článku "propojení virtuálních sítí". 

- Pomocí [Azure Portal](https://portal.azure.com/) nebo PowerShellu vytvořte bránu VPN ExpressRoute připojenou k vaší virtuální síti.
  - Pokud používáte Azure Portal, přidejte novou **bránu Virtual Network** a jako typ brány vyberte **ExpressRoute** .
  - Pokud používáte PowerShell, nejdřív si stáhněte a použijte nejnovější [sadu Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Následující příkazy vytvoří bránu ExpressRoute. Text předchází _$_ uživatelem definovaných proměnných, které by měly být aktualizovány s konkrétními informacemi.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

V tomto příkladu se použila SKU brány HighPerformance. Vaše možnosti jsou HighPerformance nebo UltraPerformance jako jediné SKU brány, které jsou podporované pro SAP HANA v Azure (velké instance).

> [!IMPORTANT]
> Pro velké instance SKU třídy typu II je nutné použít SKU brány UltraPerformance.

## <a name="link-virtual-networks"></a>Propojení virtuálních sítí

Virtuální síť Azure teď má bránu ExpressRoute. Pomocí autorizačních informací poskytnutých společností Microsoft připojte bránu ExpressRoute k okruhu Velké instance SAP HANA ExpressRoute. Můžete se připojit pomocí Azure Portal nebo PowerShellu. Pokyny k PowerShellu jsou následující. 

Pro každou bránu ExpressRoute spusťte následující příkazy s použitím různých AuthGUID pro každé připojení. První dvě položky zobrazené v následujícím skriptu pocházejí z informací poskytnutých společností Microsoft. AuthGUID je také specifický pro každou virtuální síť a bránu. Pokud chcete přidat další službu Azure Virtual Network, musíte pro okruh ExpressRoute získat další AuthID, které propojí velké instance HANA s Azure od Microsoftu. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> Poslední parametr příkazu New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** je nový parametr, který umožňuje rychlou cestu ExpressRoute. Funkce, která omezuje latenci sítě mezi jednotkami velkých instancí HANA a virtuálními počítači Azure. Tato funkce byla přidána v květnu 2019. Další podrobnosti najdete v článku [architektura sítě SAP Hana (velké instance)](./hana-network-architecture.md). Před spuštěním příkazů se ujistěte, že používáte nejnovější verze rutin PowerShellu.

Pokud chcete bránu připojit k více než jednomu okruhu ExpressRoute přidruženému k vašemu předplatnému, možná budete muset tento krok spustit více než jednou. Například pravděpodobně budete chtít připojit stejnou bránu virtuální sítě k okruhu ExpressRoute, který připojuje virtuální síť k místní síti.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Použití rychlé cesty ExpressRoute na stávající ExpressRoute okruhy velké instance HANA
Dokumentace zatím vysvětluje, jak připojit nový okruh ExpressRoute, který byl vytvořen s nasazením velké instance HANA, do brány Azure ExpressRoute jedné z vašich virtuálních sítí Azure. Ale spousta zákazníků už má nastavené ExpressRouteé okruhy a jejich virtuální sítě už jsou připojené k velkým instancím HANA. Vzhledem k tomu, že nová rychlá cesta ExpressRoute snižuje latenci sítě, doporučuje se použít tuto funkci ke změně. Příkazy pro připojení nového okruhu ExpreesRoute a změnu stávajícího okruhu ExpressRoute jsou stejné. V důsledku toho musíte spustit tuto sekvenci příkazů PowerShellu a změnit stávající okruh na použití. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Je důležité přidat poslední parametr zobrazený výše a povolit funkci ExpressRoute rychlá cesta.


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Jak chcete povolit Global Reach pro jeden nebo oba dva scénáře:

 - Replikace systému HANA bez dalších proxy a bran firewall
 - Kopírování záloh mezi jednotkami velkých instancí HANA ve dvou různých oblastech, aby se prováděly systémové kopie nebo aktualizace systému

je potřeba vzít v úvahu:

- Je potřeba zadat rozsah adresního prostoru a/29 adresního prostoru. Tento rozsah adres se nesmí překrývat s žádným z dalších rozsahů adresních prostorů, které jste použili k nasazení velkých instancí HANA do Azure, a nesmí se překrývat s žádným z rozsahů IP adres, které jste použili jinde v Azure nebo v místním prostředí.
- K dispozici je omezení čísla ASN (autonomního systému), které se dá použít k inzerování místních tras do rozsáhlých instancí HANA. Vaše místní zařízení nesmí inzerovat žádné trasy s privátním čísla ASN v rozsahu 65000 – 65020 nebo 65515. 
- Pro scénář připojení místního přímého přístupu k velkým instancím HANA musíte vypočítat poplatek za okruh, který vás připojí k Azure. Pro ceny si prohlédněte ceny za [Global REACH doplněk](https://azure.microsoft.com/pricing/details/expressroute/).

Pokud chcete získat jeden nebo oba scénáře použité pro vaše nasazení, otevřete zprávu o podpoře v Azure, jak je popsáno v tématu [otevření žádosti o podporu pro velké instance Hana](./hana-li-portal.md#open-a-support-request-for-hana-large-instances) .

Požadovaná data a klíčová slova, která potřebujete použít pro Microsoft, aby bylo možné směrovat a provádět na vaší žádosti, vypadá takto:

- Služba: SAP HANA velká instance
- Typ problému: konfigurace a nastavení
- Podtyp problému: můj problém není uvedený výše
- Subject "upravit síť – přidat Global Reach"
- Podrobnosti: ' Přidání Global Reach do systému HANA velká instance pro tenanta rozsáhlých instancí nebo přidání Global Reach k místnímu tenantovi instance HANA.
- Další podrobnosti o velkých instancích HANA pro klienta HANA velké instance: je potřeba definovat **dvě oblasti Azure** , ve kterých se oba klienti připojují **, a** potřebujete odeslat **Rozsah IP adres/29** .
- Další podrobnosti pro případ klienta s velkými instancemi v síti pro HANA: musíte definovat **oblast Azure** , ve které je nasazený tenant pro velké instance Hana, k němuž se chcete připojit přímo. Kromě toho musíte zadat **identifikátor GUID ověřování** a **partnerské číslo okruhu** , který jste dostali při navázání okruhu ExpressRoute mezi místními sítěmi a Azure. Dále musíte pojmenovat **ASN**. Poslední dodávkou je **Rozsah IP adres/29** pro ExpressRoute Global REACH.

> [!NOTE]
> Pokud chcete oba případy zpracovat, je nutné dodat dva různé rozsahy IP adres/29, které se nepřekrývají s žádným jiným použitým rozsahem IP adres. 




## <a name="next-steps"></a>Další kroky

- [Další požadavky na síť pro HLI](hana-additional-network-requirements.md)
