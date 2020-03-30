---
title: Připojení nastavené z virtuální sítě na SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Připojení nastavené z virtuální sítě pro použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617187"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Připojení virtuální sítě k velkým instancím HANA

Po vytvoření virtuální sítě Azure můžete tuto síť připojit k SAP HANA ve velkých instancích Azure. Vytvořte bránu Azure ExpressRoute ve virtuální síti. Tato brána umožňuje propojit virtuální síť s okruhem ExpressRoute, který se připojuje k tenantovi zákazníka na razítku velké instance HANA.

> [!NOTE] 
> Tento krok může trvat až 30 minut. Nová brána se vytvoří v určeném předplatném Azure a pak se připojí k zadané virtuální síti Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Pokud brána již existuje, zkontrolujte, jestli se jedná o bránu ExpressRoute nebo ne. Pokud se nejedná o bránu ExpressRoute, odstraňte bránu a znovu ji vytvořte jako bránu ExpressRoute. Pokud je brána ExpressRoute již vytvořena, přečtěte si následující část tohoto článku "Propojit virtuální sítě". 

- Pomocí [portálu Azure nebo](https://portal.azure.com/) PowerShellu vytvořte bránu ExpressRoute VPN připojenou k vaší virtuální síti.
  - Pokud používáte portál Azure, přidejte novou **bránu virtuální sítě**a pak jako typ brány vyberte **ExpressRoute.**
  - Pokud používáte PowerShell, nejprve stáhněte a použijte nejnovější [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Následující příkazy vytvoří bránu ExpressRoute. Texty, před nimiž a jsou uživatelem _$_ definované proměnné, které by měly být aktualizovány s konkrétními informacemi.

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

V tomto příkladu byla použita skladová položka brány s vysokým výkonem. Vaše možnosti jsou HighPerformance nebo UltraPerformance jako jediné brány skum, které jsou podporovány pro SAP HANA v Azure (velké instance).

> [!IMPORTANT]
> Pro velké instance HANA třídy Typu II je nutné použít skladovou položku brány UltraPerformance.

## <a name="link-virtual-networks"></a>Propojení virtuálních sítí

Virtuální síť Azure má teď bránu ExpressRoute. Pomocí informací o autorizaci poskytnutých společností Microsoft připojte bránu ExpressRoute k okruhu SAP HANA Large Instances ExpressRoute. Můžete se připojit pomocí portálu Azure nebo PowerShellu. Pokyny prostředí PowerShell jsou následující. 

Spusťte následující příkazy pro každou bránu ExpressRoute pomocí jiného AuthGUID pro každé připojení. První dvě položky uvedené v následujícím skriptu pocházejí z informací poskytnutých společností Microsoft. Také AuthGUID je specifické pro každou virtuální síť a její bránu. Pokud chcete přidat další virtuální síť Azure, musíte získat další AuthID pro okruh ExpressRoute, který spojuje velké instance HANA do Azure od Microsoftu. 

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
> Poslední parametr v příkazu New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** je nový parametr, který umožňuje ExpressRoute Rychlá cesta. Funkce, která snižuje latenci sítě mezi jednotkami velké instance HANA a virtuálními počítači Azure. Funkce byla přidána v květnu 2019. Další podrobnosti naleznete v článku [architektura sítě SAP HANA (Velké instance).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Před spuštěním příkazů se ujistěte, že používáte nejnovější verzi rutin prostředí PowerShell.

Chcete-li připojit bránu k více než jednomu okruhu ExpressRoute přidruženému k vašemu předplatnému, může být nutné spustit tento krok více než jednou. Například pravděpodobně připojíte stejnou bránu virtuální sítě k okruhu ExpressRoute, který propojuje virtuální síť s místní sítí.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Použití rychlé cesty ExpressRoute na existující okruhy HANA Large Instance ExpressRoute
Dokumentace zatím vysvětluje, jak připojit nový okruh ExpressRoute, který byl vytvořen pomocí nasazení velké instance HANA, k bráně Azure ExpressRoute jedné z vašich virtuálních sítí Azure. Ale mnoho zákazníků již má své okruhy ExpressRoute nastavení již a mají své virtuální sítě připojené k HANA velké instance již. Vzhledem k tomu, že nová rychlá cesta ExpressRoute snižuje latenci sítě, doporučujeme použít změnu k použití této funkce. Příkazy pro připojení nového okruhu ExpreesRoute a pro změnu existujícího okruhu ExpressRoute jsou stejné. V důsledku toho je třeba spustit tuto sekvenci příkazů prostředí PowerShell změnit existující obvod pro použití 

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

Je důležité přidat poslední parametr zobrazený výše, abyste povolili funkci Rychlé cesty ExpressRoute.


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Chcete-li povolit globální dosah pro jeden nebo oba dva scénáře:

 - Replikace systému HANA bez dalších proxy serverů nebo bran firewall
 - Kopírování záloh mezi jednotkami velké instance HANA ve dvou různých oblastech za účelem provedení systémových kopií nebo aktualizací systému

je třeba vzít v úvahu, že:

- Je třeba zadat rozsah adresního prostoru /29 adresního prostoru. Tento rozsah adres se nemusí překrývat s žádným jiným rozsahem adresního prostoru, který jste dosud používali při připojování velkých instancí HANA k Azure, a nemusí se překrývat s žádným rozsahem IP adres, který jste použili někde jinde v Azure nebo v místním prostředí.
- Je omezení asns (číslo autonomního systému), které lze použít k inzerování místní trasy hana velké instance. Vaše místní nesmí inzerovat žádné trasy se soukromými ASN v rozsahu 65000 – 65020 nebo 65515. 
- Pro scénář připojení místního přímého přístupu k velkým instancím HANA je potřeba vypočítat poplatek za okruh, který vás připojí k Azure. Ceny najdete v cenách [doplňku Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Chcete-li získat jeden nebo oba scénáře použité pro vaše nasazení, otevřete zprávu podpory s Azure, jak je popsáno v [otevřete žádost o podporu pro velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Data, která jsou potřebná, a klíčová slova, která je třeba použít k tomu, aby společnost Microsoft mohla na vaši žádost směrovat a provádět, vypadají takto:

- Služba: Velká instance SAP HANA
- Typ problému: Konfigurace a instalace
- Podtyp problému: Můj problém není uveden výše
- Předmět "Změnit síť – přidat globální dosah"
- Podrobnosti: "Přidat globální dosah hana velké instance hana velké instance tenanta nebo "Přidat globální dosah do místního klienta velké instance HANA.
- Další podrobnosti pro případ klienta velkého instanci HANA hana: Je třeba definovat **dvě oblasti Azure,** kde jsou umístěny dva klienti pro připojení **a** je třeba odeslat rozsah ip **adres /29**
- Další podrobnosti pro místní případ tenanta HANA velké instance: Je třeba definovat **oblast Azure,** kde je nasazen ý tenanta velké instance HANA, ke kterému se chcete připojit přímo. Kromě toho je třeba zadat **auth GUID** a **Circuit Peer ID,** které jste obdrželi při nastavování okruhu ExpressRoute mezi místní a Azure. Kromě toho je třeba pojmenovat **asn**. Poslední dodávka je **rozsah IP adres /29** pro ExpressRoute Global Reach.

> [!NOTE]
> Pokud chcete, aby byly oba případy zpracovány, je třeba zadat dva různé rozsahy ADRES /29 IP, které se nepřekrývají s žádným jiným rozsahem IP adres, který byl dosud používán. 




## <a name="next-steps"></a>Další kroky

- [Další požadavky na síť pro HLI](hana-additional-network-requirements.md)
