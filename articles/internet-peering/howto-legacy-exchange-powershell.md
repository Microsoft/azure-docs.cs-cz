---
title: Převést starší partnerský vztah Exchange na prostředek Azure pomocí PowerShellu
titleSuffix: Azure
description: Převést starší partnerský vztah Exchange na prostředek Azure pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686779"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Převést starší partnerský vztah Exchange na prostředek Azure pomocí PowerShellu

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí rutin prostředí PowerShell.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-legacy-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky](prerequisites.md) a [návod partnerského vztahu serveru Exchange.](walkthrough-exchange-all.md)

### <a name="work-with-azure-powershell"></a>Práce s Azure PowerShellem
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Převést starší partnerský vztah Exchange na prostředek Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Získání staršího partnerského vztahu exchange pro převod
Tento příklad ukazuje, jak získat starší partnerský vztah Exchange v umístění partnerského vztahu v Seattlu:

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
Tento příkaz lze použít k převodu staršího partnerského vztahu Exchange na prostředek Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Když převedete starší partnerský vztah na prostředek Azure, úpravy nejsou podporované.
&nbsp;

Tento příklad odpovědi ukazuje, když bylo úspěšně dokončeno zřizování od konce:

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
Další informace naleznete v tématech Nejčastější dotazy [k internetovému partnerovi](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu exchange pomocí PowerShellu](howto-exchange-powershell.md)
