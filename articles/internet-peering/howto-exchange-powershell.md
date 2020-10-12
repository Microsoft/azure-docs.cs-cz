---
title: Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu
titleSuffix: Azure
description: Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d098f1ff0f78e37601544619c603b776e0cf22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071750"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Exchange pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí webu Azure [Portal](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .
* Pokud už máte partnerské vztahy Exchange s Microsoftem, které se nepřevádějí do prostředků Azure, přečtěte si téma [Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Práce s Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Vytvoření a zřízení partnerského vztahu Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Získat seznam podporovaných umístění partnerských vztahů pro partnerský vztah Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Vytvoření partnerského vztahu Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Získat partnerský vztah Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Úprava partnerského vztahu Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Zrušení zřízení partnerského vztahu Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md)
* [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Další zdroje
Podrobnější popis všech parametrů získáte spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).
