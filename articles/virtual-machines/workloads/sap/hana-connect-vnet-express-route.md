---
title: Nastavení připojení z virtuální sítě k SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Nastavení připojení z virtuální sítě pro použití SAP HANA v Azure (velké instance).
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
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a64a60603cd9898386a975313afc676e3b253326
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353593"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Připojení virtuální sítě pro velké instance HANA

Po vytvoření virtuální sítě Azure, můžete připojit tuto síť k SAP HANA ve velkých instancích Azure. Vytvořte bránu Azure ExpressRoute ve virtuální síti. Tato brána umožňuje propojení virtuální sítě k okruhu ExpressRoute, který se připojuje k tenantovi zákazníka na razítku velká instance.

> [!NOTE] 
> Tento krok může trvat až 30 minut. Nová brána je vytvořené v rámci určené předplatného Azure a poté připojen k zadané virtuální síti Azure.

Pokud již existuje brána, zkontrolujte, zda je bránu ExpressRoute nebo ne. V opačném případě odstraňte bránu a znovu jej vytvořte jako bránu ExpressRoute. Pokud bránu ExpressRoute se už navázalo, naleznete v následující části tohoto článku, "Propojení virtuálních sítí." 

- Použijte buď [webu Azure portal](https://portal.azure.com/) nebo prostředí PowerShell k vytvoření brány VPN typu ExpressRoute připojené k virtuální síti.
  - Pokud použijete Azure portal, přidejte nový **brány virtuální sítě**a pak vyberte **ExpressRoute** jako typ brány.
  - Pokud používáte PowerShell, nejdřív stáhnout a použít nejnovější [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). Následující příkazy vytvoří bránu ExpressRoute. Předchází texty _$_ jsou uživatelem definované proměnné, které je třeba aktualizovat konkrétní informace.

```PowerShell
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
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

V tomto příkladu byl použit HighPerformance SKU brány. Možnosti jsou HighPerformance nebo UltraPerformance jako skladové položky pouze brány, které jsou podporovány pro SAP HANA v Azure (velké instance).

> [!IMPORTANT]
> Pro velké instance HANA SKU typu II třídy musíte použít SKU brány UltraPerformance.

## <a name="link-virtual-networks"></a>Propojení virtuálních sítí

Virtuální síť Azure teď má bránu ExpressRoute. Autorizace na základě informací poskytnutých Microsoft slouží k připojení ExpressRoute gateway do systému SAP HANA v okruhu ExpressRoute Azure (velké instance). Můžete připojit pomocí webu Azure portal nebo Powershellu. Na portálu se doporučuje, ale pokud chcete pomocí prostředí PowerShell, jsou následující pokyny. 

Spusťte následující příkazy pro každou bránu virtuální sítě s použitím různých AuthGUID pro každé připojení. První dvě položky zobrazené v následujícím skriptu pocházejí z informací od Microsoftu. Navíc AuthGUID je specifické pro každou virtuální síť a svou bránu. Pokud chcete přidat další virtuální síť Azure, budete muset získat další AuthID pro váš okruh ExpressRoute, který se připojuje velké instance HANA do Azure. 

```PowerShell
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
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Pro připojení brány pro více než jeden okruh ExpressRoute přidružených k vašemu předplatnému, můžete potřebovat ke spuštění tohoto kroku více než jednou. Například pravděpodobně budete připojení stejnou bránu virtuální sítě k okruhu ExpressRoute, který se připojuje virtuální síť k místní síti.

## <a name="next-steps"></a>Další postup

- [Další síťové požadavky pro HLI](hana-additional-network-requirements.md)