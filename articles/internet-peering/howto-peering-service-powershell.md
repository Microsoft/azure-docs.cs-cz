---
title: Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí prostředí PowerShell
titleSuffix: Azure
description: Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí prostředí PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774182"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí prostředí PowerShell

Tento článek popisuje, jak povolit [službu partnerského](overview-peering-service.md) vztahu v přímém partnerském vztahu pomocí rutin prostředí PowerShell a modelu nasazení Správce prostředků.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [portálu](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si přečtěte [předpoklady](prerequisites.md) .
* Ve vašem předplatném vyberte přímý partnerský vztah, na kterém chcete povolit službu peering Service. Pokud ho nemáte, buď převeďte starší verzi přímého partnerského vztahu nebo vytvořte nový přímý partnerský vztah.
    * Pokud chcete převést starší verzi přímého partnerského vztahu, postupujte podle pokynů v tématu [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md).
    * Pokud chcete vytvořit nový přímý partnerský vztah, postupujte podle pokynů v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby partnerského vztahu v přímém partnerském vztahu

### <a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po získání přímého partnerského vztahu v předchozím kroku ho povolte pro službu partnerského vztahu.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Pokud potřebujete změnit nastavení připojení, přečtěte si část **Úprava přímého partnerského vztahu** v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu systému Exchange pomocí prostředí PowerShell](howto-exchange-powershell.md)
* [Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Další zdroje informací:
Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Nejčastější dotazy najdete v tématu [Nejčastější dotazy ke službě peering Service](service-faqs.md).