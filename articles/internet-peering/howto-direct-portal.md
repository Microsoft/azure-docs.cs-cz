---
title: Vytvoření nebo úprava přímého partnerského vztahu pomocí Azure Portal
titleSuffix: Azure
description: Vytvoření nebo úprava přímého partnerského vztahu pomocí Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681077"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Vytvoření nebo úprava přímého partnerského vztahu pomocí Azure Portal

Tento článek popisuje, jak vytvořit přímý partnerský vztah Microsoftu pomocí Azure Portal. Tento článek také ukazuje, jak kontrolovat stav prostředku, aktualizovat ho nebo odstranit a zrušit jeho zřízení.

Pokud budete chtít, můžete tuto příručku dokončit pomocí Azure [PowerShellu](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .
* Pokud už máte přímá připojení partnerského vztahu se společností Microsoft, která nejsou převedená na prostředky Azure, přečtěte si téma [Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Vytvoření a zřízení přímého partnerského vztahu

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Vytvoření přímého partnerského vztahu

Novou žádost o vytvoření partnerského vztahu můžete vytvořit pomocí prostředku **partnerského vztahu** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Spustit prostředek a nakonfigurovat základní nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověření přímého partnerského vztahu
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Úprava přímého partnerského vztahu
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Zrušení zřízení přímého partnerského vztahu
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava partnerského vztahu serveru Exchange pomocí portálu](howto-exchange-portal.md)
* [Převedení staršího partnerského vztahu serveru Exchange na prostředek Azure pomocí portálu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).
