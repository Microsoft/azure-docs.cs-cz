---
title: Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu Azure
titleSuffix: Azure
description: Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680939"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu Azure

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Exchange pomocí portálu Azure. Tento článek také ukazuje, jak zkontrolovat stav prostředku, aktualizovat nebo odstranit a zrušit jeho zřízení.

Pokud chcete, můžete tuto příručku dokončit pomocí [prostředí PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace zkontrolujte [požadavky](prerequisites.md) a [návod partnerského vztahu serveru Exchange.](walkthrough-exchange-all.md)
* Pokud už máte partnerské vztahy Exchange s Microsoftem, které nejsou převedeny na prostředky Azure, najdete [v tématu Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Vytvoření a zřízení partnerského vztahu exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se na portál a vyberte předplatné
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Vytvoření partnerského vztahu serveru Exchange

Nový požadavek partnerského vztahu můžete vytvořit pomocí prostředku **partnerského vztahu.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Spuštění zdroje a konfigurace základních nastavení
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

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)
* [Převést starší přímý partnerský vztah na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v tématech Nejčastější dotazy [k internetovému partnerovi](faqs.md).
