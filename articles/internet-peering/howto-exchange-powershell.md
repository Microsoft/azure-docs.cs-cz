---
title: Vytvoření nebo úprava partnerského vztahu exchange pomocí PowerShellu
titleSuffix: Azure
description: Vytvoření nebo úprava partnerského vztahu exchange pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774403"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Vytvoření nebo úprava partnerského vztahu exchange pomocí PowerShellu

Tento článek popisuje, jak vytvořit partnerský vztah microsoft exchange pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky](prerequisites.md) a [partnerský vztah serveru Exchange.](walkthrough-exchange-all.md)
* V případě, že už máte partnerské vztahy Exchange s Microsoftem, které se nepřevedou na prostředky Azure, přečtěte [si odkaz na převod staršího partnerského vztahu Exchange na prostředek Azure pomocí PowerShellu.](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Vytvoření a zřízení partnerského vztahu exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Získání seznamu podporovaných partnerských umístění pro partnerský vztah Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Vytvoření partnerského vztahu serveru Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Získání partnerského vztahu exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Úprava partnerského vztahu serveru Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Zrušení zřízení partnerského vztahu serveru Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí PowerShellu](howto-direct-powershell.md)
* [Převod staršího přímého partnerského vztahu na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Další zdroje
Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)
