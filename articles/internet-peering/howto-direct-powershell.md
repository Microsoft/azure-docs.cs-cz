---
title: Vytvoření nebo úprava přímého partnerského vztahu pomocí PowerShellu
titleSuffix: Azure
description: Vytvoření nebo úprava přímého partnerského vztahu pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680796"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Vytvoření nebo úprava přímého partnerského vztahu pomocí PowerShellu

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Direct pomocí rutin prostředí PowerShell a modelu nasazení Azure Resource Manager. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-direct-portal.md)Azure .

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky](prerequisites.md) a [návod k přímému partnerského vztahu.](walkthrough-direct-all.md)
* Pokud už máte připojení přímého partnerského vztahu s Microsoftem, které nejsou převedeny na prostředky Azure, najdete [v tématu Převod staršího přímého partnerského vztahu na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Práce s Azure PowerShellem
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Vytvoření a zřízení přímého partnerského vztahu

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Získání seznamu podporovaných umístění partnerského vztahu pro přímý partnerský vztah
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Vytvoření přímého partnerského vztahu
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověřit přímý partnerský vztah
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Úprava přímého partnerského vztahu
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Zrušení zřízení přímého partnerského vztahu
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu](howto-exchange-powershell.md)
* [Převést starší partnerský vztah Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Další zdroje
Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace naleznete v tématech Nejčastější dotazy [k internetovému partnerovi](faqs.md).
