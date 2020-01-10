---
title: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu
titleSuffix: Azure
description: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775196"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Převod starší verze serveru Exchange peering na prostředek Azure pomocí portálu

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí portálu.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Převod starší verze serveru Exchange peering na prostředek Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Převést starší verzi partnerského vztahu systému Exchange

Pomocí prostředku **partnerského vztahu** můžete převádět starší verze partnerských vztahů.

#### <a name="launch-resource-and-configure-basic-settings"></a>Spustit prostředek a nakonfigurovat základní nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>Ověření partnerského vztahu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje informací:

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu](howto-exchange-portal.md)
