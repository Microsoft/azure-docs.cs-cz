---
title: Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell
titleSuffix: Azure
description: Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774234"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell

Tento článek popisuje, jak vytvořit přímý partnerský vztah Microsoftu pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [portálu](howto-direct-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [předpoklady](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .
* V případě, že už máte přímý partnerský vztah s Microsoftem, který se do prostředků Azure nepřevede, přečtěte si téma [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md) .

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Vytvoření a zřízení přímého partnerského vztahu

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>Získat seznam podporovaných umístění partnerských vztahů pro přímý partnerský vztah
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>Vytvoření přímého partnerského vztahu
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>Ověření přímého partnerského vztahu
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>Úprava přímého partnerského vztahu
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>Zrušení zřízení přímého partnerského vztahu
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu systému Exchange pomocí prostředí PowerShell](howto-exchange-powershell.md).
* [Převeďte starší verzi serveru Exchange peering na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Další zdroje informací:
Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .
