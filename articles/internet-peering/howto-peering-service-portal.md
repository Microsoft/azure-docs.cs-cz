---
title: Povolení služby Partnerského vztahu Azure v přímém partnerskému vztahu pomocí portálu Azure
titleSuffix: Azure
description: Povolení služby Partnerského vztahu Azure v přímém partnerskému vztahu pomocí portálu Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687059"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Povolení služby Partnerského vztahu Azure v přímém partnerskému vztahu pomocí portálu Azure

Tento článek popisuje, jak povolit [službu partnerského vztahu](overview-peering-service.md) Azure v přímém partnerskému vztahu pomocí portálu Azure.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky.](prerequisites.md)
* V předplatném zvolte přímý partnerský vztah, pro který chcete povolit službu partnerského vztahu. Pokud ho nemáte, převeďte starší verzi přímého partnerského vztahu nebo vytvořte nový přímý partnerský vztah:
    * Chcete-li převést starší přímý partnerský vztah, postupujte podle pokynů v [převést starší přímý partnerský vztah na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md).
    * Chcete-li vytvořit nový přímý partnerský vztah, postupujte podle pokynů v části [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby Peering Service u peeringu typu Direct

### <a name="view-direct-peering"></a><a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po otevření přímého partnerského vztahu v předchozím kroku jej povolte pro službu partnerského vztahu.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Chcete-li změnit nastavení připojení, přečtěte si část "Změna přímého partnerského vztahu" v části [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu exchange pomocí portálu](howto-exchange-portal.md)
* [Převést starší partnerský vztah Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje

Nejčastější dotazy naleznete v [nejčastějších dotazech](service-faqs.md)ke službě Partnerského vztahu .