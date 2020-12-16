---
title: Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí PowerShellu
titleSuffix: Azure
description: Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: acc32f4916f5f7f8fe22eebdd1e72db297cac94c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590201"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí PowerShellu

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí rutin PowerShellu.

Pokud chcete, můžete tuto příručku dokončit pomocí webu Azure [Portal](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .

### <a name="work-with-azure-powershell"></a>Práce s Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Získat starší verzi Exchange peering pro převod
Tento příklad ukazuje, jak získat starší partnerský vztah serveru Exchange v umístění partnerského vztahu pro Seattle:

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

### <a name="convert-legacy-peering"></a>Převod starší verze partnerského vztahu
Tento příkaz se dá použít k převedení staršího partnerského vztahu Exchange na prostředek Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Když převedete starší verzi partnerského vztahu na prostředek Azure, úpravy se nepodporují.
&nbsp;

Tato příklad reakce ukazuje, kdy bylo úspěšné dokončení kompletního zřizování:

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
## <a name="additional-resources"></a>Další zdroje informací
Podrobnější popis všech parametrů získáte spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```
Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu](howto-exchange-powershell.md)
