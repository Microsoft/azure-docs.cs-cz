---
title: Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí Azure Portal
titleSuffix: Azure
description: Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680939"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí Azure Portal

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Exchange pomocí Azure Portal. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .
* Pokud už máte partnerské vztahy Exchange s Microsoftem, které se nepřevádějí na prostředky Azure, přečtěte si téma [Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Vytvoření a zřízení partnerského vztahu Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Vytvoření partnerského vztahu Exchange

Novou žádost o vytvoření partnerského vztahu můžete vytvořit pomocí prostředku **partnerského vztahu** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Spuštění prostředku a Konfigurace základního nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Ověření partnerského vztahu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Úprava partnerského vztahu Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Zrušení zřízení partnerského vztahu Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)
* [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).
