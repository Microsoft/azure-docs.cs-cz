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
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164703"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Propojení virtuální sítě ExpressRoute velká Instance HANA

Jak je definované všechny rozsahy IP adres a nyní je teď dat zpět od Microsoftu, můžete začít připojovat virtuální síti vytvořené před na velkých instancích HANA. Po vytvoření virtuální sítě Azure v VNet k propojení virtuální sítě k okruhu ExpressRoute, který se připojuje k tenantovi zákazníka na razítku velká Instance musí být vytvořené bránu ExpressRoute.

> [!NOTE] 
> Tento krok může trvat až 30 minut na dokončení, jak nová brána je v určené předplatného Azure a pak připojíme je k zadaná virtuální síť Azure.

Pokud již existuje brána, zkontrolujte, zda je bránu ExpressRoute nebo ne. Pokud ne, brána musí odstranit a znovu vytvořen jako bránu ExpressRoute. Pokud bránu ExpressRoute je už navázat, protože virtuální síť Azure je již připojen k okruhu ExpressRoute pro místní připojení, přejděte k níže v části propojení virtuálních sítí.

- Použít buď (nové) [webu Azure portal](https://portal.azure.com/), nebo prostředí PowerShell k vytvoření brány VPN typu ExpressRoute připojené k virtuální síti.
  - Pokud použijete Azure portal, přidejte nový **brány virtuální sítě** a pak vyberte **ExpressRoute** jako typ brány.
  - Pokud jste zvolili místo toho prostředí PowerShell, nejdřív stáhnout a použít nejnovější [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) zajistit optimální výkon. Následující příkazy vytvoří bránu ExpressRoute. Předchází texty _$_ jsou uživatelem definované proměnné, které je třeba aktualizovat pomocí konkrétní informace.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

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

V tomto příkladu byl použit HighPerformance SKU brány. Možnosti jsou HighPerformance nebo UltraPerformance jako pouze brána SKU, které jsou podporovány pro SAP HANA v Azure (velké instance).

> [!IMPORTANT]
> Pro velké instance HANA z typu II classs SKU je povinné použití SKU brány UltraPerformance.

**Propojení virtuálních sítí**

Teď, když bránu ExpressRoute má virtuální síť Azure, použijte informace o ověření od Microsoftu pro připojení k SAP HANA na okruh Azure ExpressRoute (velké instance) pro toto připojení vytvořit bránu ExpressRoute. Tento krok můžete provést pomocí webu Azure portal nebo Powershellu. Na portálu se doporučuje, ale jsou následující pokyny pro PowerShell. 

- Můžete spustit následující příkazy pro každou bránu virtuální sítě pomocí různých AuthGUID pro každé připojení. První dvě položky zobrazené v následujícím skriptu pocházejí z informací od Microsoftu. Navíc AuthGUID je specifická pro každou virtuální síť a svou bránu. Prostředky, pokud chcete přidat jiné virtuální síti Azure, potřebujete získat další AuthID pro váš okruh ExpressRoute, který se připojuje velké instance HANA do Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
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

Pokud se chcete připojit bránu k více okruhům ExpressRoute, které jsou spojené s vaším předplatným, budete muset provést tento krok více než jednou. Například pravděpodobně budete připojení stejné brány virtuální sítě k okruhu ExpressRoute, který se připojuje virtuální síť k místní síti.

**Další kroky**

- Přečtěte si [další síťové požadavky pro HLI](hana-additional-network-requirements.md).