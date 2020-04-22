---
title: Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu Azure
titleSuffix: Azure
description: Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681077"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu Azure

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Direct pomocí portálu Azure. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí Azure [PowerShellu](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky](prerequisites.md) a [návod k přímému partnerského vztahu.](walkthrough-direct-all.md)
* Pokud už máte připojení přímého partnerského vztahu s Microsoftem, které nejsou převedeny na prostředky Azure, najdete [v tématu Převod staršího přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Vytvoření a zřízení přímého partnerského vztahu

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
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

* [Vytvoření nebo úprava partnerského vztahu exchange pomocí portálu](howto-exchange-portal.md)
* [Převést starší partnerský vztah Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v tématech Nejčastější dotazy [k internetovému partnerovi](faqs.md).
