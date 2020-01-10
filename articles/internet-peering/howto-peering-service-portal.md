---
title: Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí portálu
titleSuffix: Azure
description: Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774975"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Povolení služby partnerského vztahu v přímém partnerském vztahu pomocí portálu

Tento článek popisuje, jak povolit [službu partnerského](overview-peering-service.md) vztahu v přímém partnerském vztahu pomocí portálu.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si přečtěte [předpoklady](prerequisites.md) .
* Ve vašem předplatném vyberte přímý partnerský vztah, na kterém chcete povolit službu peering Service. Pokud ho nemáte, buď převeďte starší verzi přímého partnerského vztahu nebo vytvořte nový přímý partnerský vztah.
    * Pokud chcete převést starší verzi přímého partnerského vztahu, postupujte podle pokynů v tématu [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md).
    * Pokud chcete vytvořit nový přímý partnerský vztah, postupujte podle pokynů v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby partnerského vztahu v přímém partnerském vztahu

### <a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po otevření přímého partnerského vztahu v předchozím kroku ho povolte pro službu partnerského vztahu.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Pokud potřebujete změnit nastavení připojení, přečtěte si část **Úprava přímého partnerského vztahu** v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](howto-exchange-portal.md)
* [Převod starší verze serveru Exchange peering na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje informací:

Nejčastější dotazy najdete v tématu [Nejčastější dotazy ke službě peering Service](service-faqs.md).