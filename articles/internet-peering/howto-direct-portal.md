---
title: Vytvoření nebo úprava peeringu typu Direct s využitím portálu
titleSuffix: Azure
description: Vytvoření nebo úprava peeringu typu Direct s využitím portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775326"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Vytvoření nebo úprava peeringu typu Direct s využitím portálu

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Direct pomocí portálu. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace [zkontrolujte požadavky](prerequisites.md) a [přímý partnerský vztah.](walkthrough-direct-all.md)
* V případě, že už máte přímý partnerský vztah s Microsoftem, které se nepřevedou na prostředky Azure, přečtěte [si odkaz převést starší přímý partnerský vztah na prostředek Azure pomocí portálu.](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Vytvoření a zřízení přímého partnerského vztahu

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Vytvoření přímého partnerského vztahu

Nový požadavek partnerského vztahu můžete vytvořit pomocí prostředku **partnerského vztahu.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Spuštění zdroje a konfigurace základních nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověřit přímý partnerský vztah
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Úprava přímého partnerského vztahu
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Zrušení zřízení přímého partnerského vztahu
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvořte nebo upravte partnerský vztah serveru Exchange pomocí portálu](howto-exchange-portal.md).
* [Převeďte starší partnerský vztah Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)
