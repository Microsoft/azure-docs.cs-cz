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
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774234"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Vytvoření nebo úprava přímého partnerského vztahu pomocí PowerShellu

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Direct pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-direct-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky](prerequisites.md) a [přímý partnerský vztah.](walkthrough-direct-all.md)
* V případě, že už máte přímý partnerský vztah s Microsoftem, které se nepřevedou na prostředky Azure, přečtěte [si odkaz převést starší přímý partnerský vztah na prostředek Azure pomocí PowerShellu.](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem
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

* [Vytvořte nebo upravte partnerský vztah serveru Exchange pomocí prostředí PowerShell](howto-exchange-powershell.md).
* [Převést starší partnerský vztah Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Další zdroje
Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)
