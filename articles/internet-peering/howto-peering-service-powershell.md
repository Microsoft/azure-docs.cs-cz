---
title: Povolení služby Azure peering Service v přímém partnerském vztahu pomocí prostředí PowerShell
titleSuffix: Azure
description: Povolení služby Azure peering Service v přímém partnerském vztahu pomocí prostředí PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686973"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Povolení služby Azure peering Service v přímém partnerském vztahu pomocí prostředí PowerShell

Tento článek popisuje, jak povolit [službu Azure peering Service](overview-peering-service.md) v přímém partnerském vztahu pomocí rutin prostředí PowerShell a modelu nasazení Azure Resource Manager.

Pokud chcete, můžete tuto příručku dokončit pomocí webu Azure [Portal](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si přečtěte [předpoklady](prerequisites.md) .
* V předplatném vyberte přímý partnerský vztah, pro který chcete povolit službu peering Service. Pokud ho nemáte, buď převeďte starší verzi přímého partnerského vztahu, nebo vytvořte nový přímý partnerský vztah:
    * Pokud chcete převést starší verzi přímého partnerského vztahu, postupujte podle pokynů v tématu [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí PowerShellu](howto-legacy-direct-powershell.md).
    * Pokud chcete vytvořit nový přímý partnerský vztah, postupujte podle pokynů v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Práce s Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby Peering Service u peeringu typu Direct

### <a name="view-direct-peering"></a><a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po získání přímého partnerského vztahu v předchozím kroku ho povolte pro službu peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Pokud potřebujete změnit nastavení připojení, přečtěte si část "Změna přímého partnerského vztahu" v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí prostředí PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu systému Exchange pomocí prostředí PowerShell](howto-exchange-powershell.md)
* [Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí PowerShellu](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Další zdroje
Podrobnější popis všech parametrů získáte spuštěním následujícího příkazu:

```powershell
Get-Help Get-AzPeering -detailed
```

Nejčastější dotazy najdete v NEJČASTĚJŠÍch dotazech ke [službě peering](service-faqs.md).