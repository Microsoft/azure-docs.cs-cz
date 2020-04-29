---
title: Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí Azure Portal
titleSuffix: Azure
description: Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678536"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí Azure Portal

Tento článek popisuje, jak převést existující starší partnerský vztah Exchange na prostředek Azure pomocí Azure Portal.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [partnerský vztah Exchange](walkthrough-exchange-all.md) .

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Převést starší verzi partnerského vztahu systému Exchange

Starší připojení partnerských vztahů můžete převést pomocí prostředku **partnerského vztahu** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Spuštění prostředku a Konfigurace základního nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Ověření partnerského vztahu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu Exchange pomocí portálu](howto-exchange-portal.md)
