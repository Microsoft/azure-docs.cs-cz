---
title: Připojení k SAP HANA v Azure (velké instance) nastavit z virtuální sítě | Dokumentace Microsoftu
description: Připojení k nastavení z virtuální sítě pro použití SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239471"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Připojení virtuální sítě pro velké instance HANA

Po vytvoření virtuální sítě Azure, můžete připojit tuto síť k SAP HANA ve velkých instancích Azure. Vytvořte bránu Azure ExpressRoute ve virtuální síti. Tato brána umožňuje propojení virtuální sítě k okruhu ExpressRoute, který se připojuje k tenantovi zákazníka na razítku velká Instance HANA.

> [!NOTE] 
> Tento krok může trvat až 30 minut. Nová brána je vytvořené v rámci určené předplatného Azure a poté připojen k zadané virtuální síti Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Pokud již existuje brána, zkontrolujte, zda je bránu ExpressRoute nebo ne. Pokud není bránu ExpressRoute, odstraňte bránu a znovu jej vytvořte jako bránu ExpressRoute. Pokud bránu ExpressRoute se už navázalo, naleznete v následující části tohoto článku, "Propojení virtuálních sítí." 

- Použijte buď [webu Azure portal](https://portal.azure.com/) nebo prostředí PowerShell k vytvoření brány VPN typu ExpressRoute připojené k virtuální síti.
  - Pokud použijete Azure portal, přidejte nový **brány virtuální sítě**a pak vyberte **ExpressRoute** jako typ brány.
  - Pokud používáte PowerShell, nejdřív stáhnout a použít nejnovější [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). 
 
Následující příkazy vytvoří bránu ExpressRoute. Předchází texty _$_ jsou uživatelem definované proměnné, které je třeba aktualizovat konkrétní informace.

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

V tomto příkladu byl použit HighPerformance SKU brány. Možnosti jsou HighPerformance nebo UltraPerformance jako skladové položky pouze brány, které jsou podporovány pro SAP HANA v Azure (velké instance).

> [!IMPORTANT]
> Pro velké instance HANA SKU typu II třídy musíte použít SKU brány UltraPerformance.

## <a name="link-virtual-networks"></a>Propojení virtuálních sítí

Virtuální síť Azure teď má bránu ExpressRoute. Připojit bránu ExpressRoute s okruhem ExpressRoute velké instance SAP HANA pomocí autorizačních informací od Microsoftu. Můžete připojit pomocí webu Azure portal nebo Powershellu. Pokyny pro PowerShell jsou následující. 

Spusťte následující příkazy pro každou bránu ExpressRoute s použitím různých AuthGUID pro každé připojení. První dvě položky zobrazené v následujícím skriptu pocházejí z informací od Microsoftu. Navíc AuthGUID je specifické pro každou virtuální síť a svou bránu. Pokud chcete přidat další virtuální síť Azure, budete muset získat další AuthID pro váš okruh ExpressRoute, který se připojuje velké instance HANA do Azure od Microsoftu. 

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
> Poslední parametr v příkazu New-AzVirtualNetworkGatewayConnection **ExpressRouteGatewayBypass** je nový parametr, který umožňuje rychlé cesty ExpressRoute. Funkce, která organizacím snižuje latence sítě mezi velká Instance HANA jednotky a virtuální počítače Azure. Funkce je teď včteně. května 2019. Další podrobnosti najdete v článku [Síťová architektura SAP HANA (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Ujistěte se, že používáte nejnovější verzi rutin Powershellu před spuštěním příkazů.

Pro připojení brány pro více než jeden okruh ExpressRoute přidružených k vašemu předplatnému, můžete potřebovat ke spuštění tohoto kroku více než jednou. Například pravděpodobně budete připojení stejnou bránu virtuální sítě k okruhu ExpressRoute, který se připojuje virtuální síť k místní síti.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Použití ExpressRoute rychlé cestu pro existující okruhy ExpressRoute velké Instance HANA
Dokumentace, pokud bylo vysvětleno, jak připojit nový okruh ExpressRoute, který nebyl vytvořen s nasazením velká Instance HANA na Azure ExpressRoute gateway jednoho virtuálním sítím Azure. Ale mnoho zákazníků již už máte svoje nastavení okruhy ExpressRoute a mají své virtuální sítě připojen k velké instance HANA již. Jako nová ExpressRoute Rychlá cesta snižuje latence sítě, doporučujeme uplatňovat změnu, aby používala tuto funkci. Příkazy pro připojení nového okruhu ExpreesRoute a chcete-li změnit existující okruh ExpressRoute jsou stejné. Díky tomu je potřeba spustit toto pořadí příkazů prostředí PowerShell, chcete-li změnit existující okruh k použití 

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

Je důležité přidat posledního parametru, jak se zobrazuje nad k povolení funkcí ExpressRoute rychlé cesty


## <a name="expressroute-global-reach"></a>ExpressRoute globálním dosahem
Jak budete chtít povolit globální dosah pro jeden nebo oba z těchto dvou scénářů:

 - Systémové replikace HANA bez jakékoli další proxy nebo brány firewall
 - Kopírování záloh mezi jednotkami velká Instance HANA ve dvou různých oblastech provádět kopie systému nebo aktualizace systému

je třeba zvážit, který:

- Je potřeba zadat rozsah adres místo minimální velikostí/29 adresní prostor. Že rozsah adres se nemohou překrývat s některou z dalších rozsahů adresních prostorů, které jste použili zatím velkých instancích HANA připojení k Azure a se nemohou překrývat s žádným z rozsahů IP adres můžete použít někde jinde v Azure nebo místní.
- Existuje omezení na čísla ASN (číslo autonomního systému), které lze použít k inzerování místních trasy pro velké instance HANA. Místní nesmíte inzerovat všechny trasy s privátní čísla ASN v rozsahu 65000 – 65020 nebo 65515. 
- Pro scénář místní přímý přístup k připojování k HANA velkých instancí musíte k výpočtu poplatků pro okruh, který vás připojí k Azure. Ceny najdete ceny za [globální oslovit doplněk](https://azure.microsoft.com/pricing/details/expressroute/).

Chcete-li získat jeden nebo oba scénáře použitý pro vaše nasazení, otevřete podpory zprávy s Azure a jak je popsáno v [žádost o podporu pro velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Data, která je potřeba a klíčová slova, která budete muset použít pro Microsoft, abyste mohli směrovat a spustit na vaši žádost vypadá takto:

- Služba: Velká instance SAP HANA
- Typ problému: Konfigurace a nastavení
- Podtyp problému: Můj problém tu není uvedený
- Na základě práv subjektů "Upravit vlastní síť – přidat globální dosah.
- Podrobnosti: "Doplnit globální dosah velká Instance HANA pro velké Instance HANA tenanta nebo" přidat globální přístup k místním velká Instance HANA tenanta.
- Další podrobnosti pro velké Instance HANA na tenanta písmena velká Instance HANA: Je třeba definovat **dvou oblastech Azure** kde se nachází dva klienty pro připojení **a** budete potřebovat k odeslání   **/29 rozsah IP adres**
- Další podrobnosti pro místní na tenanta písmena velká Instance HANA: Je třeba definovat **oblast Azure** tam, kde tenanta velká Instance HANA je nasazený chcete připojit přímo. Kromě toho je potřeba zadat **Auth GUID** a **Circuit ID partnera** jste obdrželi po vytvoření váš okruh ExpressRoute mezi místní a Azure. Kromě toho potřebujete název vašeho **ASN**. Poslední dodávky je **/29 rozsah IP adres** pro ExpressRoute globální dosah.

> [!NOTE]
> Pokud chcete mít oba případy zpracovat, budete muset zadat dvě různé/29 rozsahy IP adres, které se nepřekrývají s další IP adresa rozsahu zatím používá. 




## <a name="next-steps"></a>Další postup

- [Další síťové požadavky pro HLI](hana-additional-network-requirements.md)
