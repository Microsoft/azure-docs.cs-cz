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
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678614"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Vytvoření nebo úprava partnerského vztahu exchange pomocí PowerShellu

Tento článek popisuje, jak vytvořit partnerský vztah microsoft exchange pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky](prerequisites.md) a [návod partnerského vztahu serveru Exchange.](walkthrough-exchange-all.md)
* Pokud už máte partnerské vztahy Exchange s Microsoftem, které se nepřevedou na prostředky Azure, přečtěte si, že [pomocí PowerShellu najdete v tématu Převod staršího partnerského vztahu Exchange na prostředek Azure.](howto-legacy-exchange-powershell.md)

### <a name="work-with-azure-powershell"></a>Práce s Azure PowerShellem
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
* [Převést starší přímý partnerský vztah na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Další zdroje
Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace naleznete v tématech Nejčastější dotazy [k internetovému partnerovi](faqs.md).
