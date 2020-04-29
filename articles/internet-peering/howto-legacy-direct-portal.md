---
title: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí Azure Portal
titleSuffix: Azure
description: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678826"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí Azure Portal

Tento článek popisuje, jak převést stávající starší verzi přímého partnerského vztahu na prostředek Azure pomocí Azure Portal.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Před zahájením
* Před zahájením konfigurace si Projděte návod [požadavky](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Převod starší verze přímého partnerského vztahu

Starší připojení partnerských vztahů můžete převést pomocí prostředku **partnerského vztahu** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Spuštění prostředku a Konfigurace základního nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Ověření přímého partnerského vztahu
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v tématu [Nejčastější dotazy k internetovým partnerům](faqs.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md)
