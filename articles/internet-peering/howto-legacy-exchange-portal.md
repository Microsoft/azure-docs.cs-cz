---
title: Převod starší verze peeringu typu Direct na prostředek Azure s využitím portálu
titleSuffix: Azure
description: Převod starší verze peeringu typu Direct na prostředek Azure s využitím portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775196"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Převod starší verze peeringu typu Exchange na prostředek Azure s využitím portálu

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí portálu.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky](prerequisites.md) a [partnerský vztah serveru Exchange.](walkthrough-exchange-all.md)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Převod starší verze peeringu typu Exchange na prostředek Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Převod staršího partnerského vztahu exchange

Starší připojení partnerského vztahu můžete převést pomocí prostředku **partnerského vztahu.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Spuštění zdroje a konfigurace základních nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Ověření partnerského vztahu serveru Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava peeringu typu Exchange s využitím portálu](howto-exchange-portal.md)
