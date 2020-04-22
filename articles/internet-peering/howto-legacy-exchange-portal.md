---
title: Převést starší partnerský vztah Exchange na prostředek Azure pomocí portálu Azure
titleSuffix: Azure
description: Převést starší partnerský vztah Exchange na prostředek Azure pomocí portálu Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678536"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Převést starší partnerský vztah Exchange na prostředek Azure pomocí portálu Azure

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí portálu Azure.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky](prerequisites.md) a [návod partnerského vztahu serveru Exchange.](walkthrough-exchange-all.md)

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Převést starší partnerský vztah Exchange na prostředek Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Převod staršího partnerského vztahu exchange

Starší připojení partnerského vztahu můžete převést pomocí prostředku **partnerského vztahu.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Spuštění zdroje a konfigurace základních nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Ověření partnerského vztahu serveru Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v tématech Nejčastější dotazy [k internetovému partnerovi](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](howto-exchange-portal.md)
