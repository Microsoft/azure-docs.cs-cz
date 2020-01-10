---
title: Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu
titleSuffix: Azure
description: Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774572"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu

Tento článek popisuje, jak vytvořit partnerský vztah Microsoft Exchange pomocí portálu. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .
* V případě, že máte partnerské vztahy Exchange s Microsoftem, které se už nepřevádí na prostředky Azure, přečtěte si téma [Převod staršího partnerského vztahu Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md) .

## <a name="create-and-provision-an-exchange-peering"></a>Vytvoření a zřízení partnerského vztahu Exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Vytvoření partnerského vztahu Exchange

Nový požadavek partnerských vztahů můžete vytvořit pomocí prostředku **partnerského vztahu** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Spustit prostředek a nakonfigurovat základní nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Ověření partnerského vztahu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Úprava partnerského vztahu Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Zrušení zřízení partnerského vztahu Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)
* [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Další zdroje informací:

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .
