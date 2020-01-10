---
title: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu
titleSuffix: Azure
description: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775014"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu

Tento článek popisuje, jak převést existující starší verzi přímého partnerského vztahu na prostředek Azure pomocí rutin PowerShellu.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [portálu](howto-legacy-direct-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [předpoklady](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Získat starší verzi přímých partnerských vztahů pro převod
Níže je příklad, jak získat starší verze přímého partnerského vztahu v Seattlu umístění pro partnerský vztah.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Níže je uvedená příklad odpovědi:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-legacy-direct-peering"></a>Převést starší verze přímých partnerských vztahů

&nbsp;
> [!IMPORTANT]
> Pamatujte na to, že při převodu staršího partnerského vztahu na prostředek Azure se úpravy nepodporují. &nbsp;

Pomocí níže uvedeného příkazu převeďte starší verze přímého partnerského vztahu na prostředek Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Níže je uvedená příklad odpovědi:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Další zdroje informací:
Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md).
