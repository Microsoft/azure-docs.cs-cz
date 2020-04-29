---
title: Povolení služby Azure peering Service v přímém partnerském vztahu pomocí Azure Portal
titleSuffix: Azure
description: Povolení služby Azure peering Service v přímém partnerském vztahu pomocí Azure Portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687059"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Povolení služby Azure peering Service v přímém partnerském vztahu pomocí Azure Portal

Tento článek popisuje, jak povolit [službu Azure peering Service](overview-peering-service.md) v přímém partnerském vztahu pomocí Azure Portal.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si přečtěte [předpoklady](prerequisites.md) .
* V předplatném vyberte přímý partnerský vztah, pro který chcete povolit službu peering Service. Pokud ho nemáte, buď převeďte starší verzi přímého partnerského vztahu, nebo vytvořte nový přímý partnerský vztah:
    * Pokud chcete převést starší verzi přímého partnerského vztahu, postupujte podle pokynů v tématu [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md).
    * Pokud chcete vytvořit nový přímý partnerský vztah, postupujte podle pokynů v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Povolení služby Peering Service u peeringu typu Direct

### <a name="view-direct-peering"></a><a name= get></a>Zobrazit přímý partnerský vztah
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Povolení přímého partnerského vztahu pro službu partnerského vztahu

Po otevření přímého partnerského vztahu v předchozím kroku ho povolte pro službu peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Úprava přímého připojení partnerského vztahu

Pokud chcete upravit nastavení připojení, přečtěte si část "Změna přímého partnerského vztahu" v tématu [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](howto-exchange-portal.md)
* [Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje

Nejčastější dotazy najdete v NEJČASTĚJŠÍch dotazech ke [službě peering](service-faqs.md).