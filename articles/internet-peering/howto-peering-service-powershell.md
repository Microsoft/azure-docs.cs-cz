---
title: Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí Prostředí PowerShell
titleSuffix: Azure
description: Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí Prostředí PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774182"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí Prostředí PowerShell

Tento článek popisuje, jak povolit [službu partnerského vztahu](overview-peering-service.md) v přímém partnerské masce pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky.](prerequisites.md)
* V předplatném zvolte přímý partnerský vztah, na kterém chcete povolit službu partnerského vztahu. Pokud ho nemáte, převeďte starší verzi přímého partnerského vztahu nebo vytvořte nový přímý partnerský vztah.
    * Chcete-li převést starší přímý partnerský vztah, postupujte podle pokynů v [převést starší přímý partnerský vztah na prostředek Azure pomocí Prostředí PowerShell](howto-legacy-direct-powershell.md).
    * Chcete-li vytvořit nový přímý partnerský vztah, postupujte podle pokynů v části [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby Peering Service u peeringu typu Direct

### <a name="view-direct-peering"></a><a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po získání přímého partnerského vztahu v předchozím kroku jej povolte pro službu partnerského vztahu.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Pokud potřebujete změnit nastavení připojení, podívejte se na změnit oddíl **Přímé partnerské vztahu** v [části Vytvořit nebo upravit přímý partnerský vztah pomocí Prostředí PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu](howto-exchange-powershell.md)
* [Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Další zdroje
Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Nejčastější dotazy najdete v [tématu Nejčastější dotazy ke službě Peering Service](service-faqs.md).