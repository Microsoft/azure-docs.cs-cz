---
title: Vytvoření nebo úprava peeringu typu Exchange s využitím portálu
titleSuffix: Azure
description: Vytvoření nebo úprava peeringu typu Exchange s využitím portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774572"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Vytvoření nebo úprava peeringu typu Exchange s využitím portálu

Tento článek popisuje, jak vytvořit partnerský vztah serveru Microsoft Exchange pomocí portálu. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky](prerequisites.md) a [partnerský vztah serveru Exchange.](walkthrough-exchange-all.md)
* V případě, že už máte partnerské vztahy Exchange s Microsoftem, které se nepřevedou na prostředky Azure, přečtěte [si odkaz na převod staršího partnerského vztahu Exchange na prostředek Azure pomocí portálu.](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Vytvoření a zřízení partnerského vztahu exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Vytvoření partnerského vztahu serveru Exchange

Nový požadavek partnerského vztahu můžete vytvořit pomocí prostředku **partnerského vztahu.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Spuštění zdroje a konfigurace základních nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Ověření partnerského vztahu serveru Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Úprava partnerského vztahu serveru Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Zrušení zřízení partnerského vztahu serveru Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava peeringu typu Direct s využitím portálu](howto-direct-portal.md)
* [Převod starší verze peeringu typu Direct na prostředek Azure s využitím portálu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)
