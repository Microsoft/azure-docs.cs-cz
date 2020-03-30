---
title: Povolení služby Peering Service u peeringu typu Direct s využitím portálu
titleSuffix: Azure
description: Povolení služby Peering Service u peeringu typu Direct s využitím portálu
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129972"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Povolení služby Peering Service u peeringu typu Direct s využitím portálu

Tento článek popisuje, jak povolit [službu partnerského vztahu](overview-peering-service.md) v přímém partnerskému vztahu pomocí portálu.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky.](prerequisites.md)
* V předplatném zvolte přímý partnerský vztah, na kterém chcete povolit službu partnerského vztahu. Pokud ho nemáte, převeďte starší verzi přímého partnerského vztahu nebo vytvořte nový přímý partnerský vztah.
    * Chcete-li převést starší přímý partnerský vztah, postupujte podle pokynů v [převést starší přímý partnerský vztah na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md).
    * Chcete-li vytvořit nový přímý partnerský vztah, postupujte podle pokynů v části [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby Peering Service u peeringu typu Direct

### <a name="view-direct-peering"></a><a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po otevření přímého partnerského vztahu v předchozím kroku jej povolte pro službu partnerského vztahu.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Pokud potřebujete změnit nastavení připojení, podívejte se na změnit oddíl **Přímé partnerské vztahu** v [části Vytvořit nebo upravit přímý partnerský vztah pomocí portálu](howto-direct-portal.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu](howto-exchange-portal.md)
* [Převod starší verze peeringu typu Exchange na prostředek Azure s využitím portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje

Nejčastější dotazy najdete v [tématu Nejčastější dotazy ke službě Peering Service](service-faqs.md).