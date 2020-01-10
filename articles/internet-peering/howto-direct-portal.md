---
title: Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu
titleSuffix: Azure
description: Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775326"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu

Tento článek popisuje, jak vytvořit přímý partnerský vztah Microsoftu pomocí portálu. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [předpoklady](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .
* V případě, že už máte přímý partnerský vztah s Microsoftem, který není převedený na prostředky Azure, přečtěte si téma [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md) .

## <a name="create-and-provision-a-direct-peering"></a>Vytvoření a zřízení přímého partnerského vztahu

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Vytvoření přímého partnerského vztahu

Nový požadavek partnerských vztahů můžete vytvořit pomocí prostředku **partnerského vztahu** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Spustit prostředek a nakonfigurovat základní nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>Ověření přímého partnerského vztahu
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>Úprava přímého partnerského vztahu
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>Zrušení zřízení přímého partnerského vztahu
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvořte nebo upravte partnerský vztah Exchange pomocí portálu](howto-exchange-portal.md).
* [Převeďte starší verzi serveru Exchange peering na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Další zdroje informací:

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .
