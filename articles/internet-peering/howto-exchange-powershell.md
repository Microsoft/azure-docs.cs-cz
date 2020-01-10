---
title: Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu
titleSuffix: Azure
description: Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774403"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Exchange pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [portálu](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .
* V případě, že máte partnerské vztahy Exchange s Microsoftem, které se už nepřevádí na prostředky Azure, přečtěte si téma [Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md) .

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Vytvoření a zřízení partnerského vztahu Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>Získat seznam podporovaných umístění partnerských vztahů pro partnerský vztah Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Vytvoření partnerského vztahu Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Získat partnerský vztah Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Úprava partnerského vztahu Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Zrušení zřízení partnerského vztahu Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md)
* [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Další zdroje informací:
Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .
