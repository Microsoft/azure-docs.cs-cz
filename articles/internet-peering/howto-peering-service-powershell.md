---
title: Povolení služby Partnerského vztahu Azure v přímém partnerské mincovním vztahu pomocí PowerShellu
titleSuffix: Azure
description: Povolení služby Partnerského vztahu Azure v přímém partnerské mincovním vztahu pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686973"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Povolení služby Partnerského vztahu Azure v přímém partnerské mincovním vztahu pomocí PowerShellu

Tento článek popisuje, jak povolit [službu partnerského vztahu](overview-peering-service.md) Azure v přímém partnerské mincovním vztahu pomocí rutin Prostředí PowerShell a modelu nasazení Azure Resource Manageru.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-peering-service-portal.md)Azure .

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky.](prerequisites.md)
* V předplatném zvolte přímý partnerský vztah, pro který chcete povolit službu partnerského vztahu. Pokud ho nemáte, převeďte starší verzi přímého partnerského vztahu nebo vytvořte nový přímý partnerský vztah:
    * Chcete-li převést starší přímý partnerský vztah, postupujte podle pokynů v [převést starší přímý partnerský vztah na prostředek Azure pomocí Prostředí PowerShell](howto-legacy-direct-powershell.md).
    * Chcete-li vytvořit nový přímý partnerský vztah, postupujte podle pokynů v části [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Práce s Azure PowerShellem
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby Peering Service u peeringu typu Direct

### <a name="view-direct-peering"></a><a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po získání přímého partnerského vztahu v předchozím kroku jej povolte pro službu partnerského vztahu.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Pokud potřebujete změnit nastavení připojení, přečtěte si část "Změnit přímý partnerský vztah" v [tématu Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí PowerShellu](howto-exchange-powershell.md)
* [Převést starší partnerský vztah Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Další zdroje
Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Nejčastější dotazy naleznete v [nejčastějších dotazech](service-faqs.md)ke službě Partnerského vztahu .