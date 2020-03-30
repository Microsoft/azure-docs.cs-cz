---
title: Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí PowerShellu
titleSuffix: Azure
description: Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775391"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí PowerShellu

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí rutin prostředí PowerShell.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky](prerequisites.md) a [partnerský vztah serveru Exchange.](walkthrough-exchange-all.md)

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Převod starší verze peeringu typu Exchange na prostředek Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Získání staršího partnerského vztahu exchange pro převod
Níže je uveden příklad získání staršího partnerského vztahu Exchange v umístění partnerského vztahu v Seattlu:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Odpověď vypadá podobně jako v následujícím příkladu:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Převést starší partnerský vztah
Příkaz Pod můžete použít k převodu staršího partnerského vztahu Exchange na prostředek Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Všimněte si, že při převodu staršípartnerský vztah na prostředek Azure, změny nejsou podporovány&nbsp;

Níže je příklad odpovědi po úspěšném dokončení zřizování od konce:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Další zdroje
Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```
Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu exchange pomocí PowerShellu](howto-exchange-powershell.md)
