---
title: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu
titleSuffix: Azure
description: Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775053"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure pomocí portálu

Tento článek popisuje, jak převést existující starší verzi přímého partnerského vztahu na prostředek Azure pomocí portálu.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [PowerShellu](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Než začnete
* Před zahájením konfigurace si Projděte návod [předpoklady](prerequisites.md) a [přímý partnerský vztah](walkthrough-direct-all.md) .


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Převod starší verze přímého partnerského vztahu na prostředek Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Přihlaste se k portálu a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Převést starší verze přímých partnerských vztahů

Pomocí prostředku **partnerského vztahu** můžete převádět starší verze partnerských vztahů.

#### <a name="launch-resource-and-configure-basic-settings"></a>Spustit prostředek a nakonfigurovat základní nastavení
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurace připojení a odeslání
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name=get></a>Ověření přímého partnerského vztahu
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Další zdroje informací:

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava přímého partnerského vztahu pomocí portálu](howto-direct-portal.md).
