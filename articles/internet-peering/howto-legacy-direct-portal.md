---
title: Převést starší přímý partnerský vztah na prostředek Azure pomocí portálu Azure
titleSuffix: Azure
description: Převést starší přímý partnerský vztah na prostředek Azure pomocí portálu Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678826"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Převést starší přímý partnerský vztah na prostředek Azure pomocí portálu Azure

Tento článek popisuje, jak převést existující starší přímé partnerského vztahu na prostředek Azure pomocí portálu Azure.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky](prerequisites.md) a [návod k přímému partnerského vztahu.](walkthrough-direct-all.md)


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Převést starší přímý partnerský vztah na prostředek Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Převedení staršího přímého partnerského vztahu

Starší připojení partnerského vztahu můžete převést pomocí prostředku **partnerského vztahu.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Spuštění zdroje a konfigurace základních nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověřit přímý partnerský vztah
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v tématech Nejčastější dotazy [k internetovému partnerovi](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)
