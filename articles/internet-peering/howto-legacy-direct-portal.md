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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775053"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Převod starší verze peeringu typu Direct na prostředek Azure s využitím portálu

Tento článek popisuje, jak převést existující starší přímý partnerský vztah na prostředek Azure pomocí portálu.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky](prerequisites.md) a [přímý partnerský vztah.](walkthrough-direct-all.md)


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Převést starší verze přímého partnerského vztahu na prostředek Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Převést starší verze přímého partnerského vztahu

Starší připojení partnerského vztahu můžete převést pomocí prostředku **partnerského vztahu.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Spuštění zdroje a konfigurace základních nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověřit přímý partnerský vztah
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)

## <a name="next-steps"></a>Další kroky

* [Vytvořte nebo upravte přímý partnerský vztah pomocí portálu](howto-direct-portal.md).
