---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a3b9160f1fb28c68bdbaa540c78b2890dbcab82
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812669"
---
* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../articles/virtual-network/quick-create-portal.md).

* Vaše virtuální síť nemá žádné brány virtuální sítě. Pokud má vaše virtuální síť bránu (buď VPN, nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě místo toho připojovaly k virtuální bráně WAN hub.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Centrum je virtuální síť, kterou vytváří a používá virtuální síť WAN. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
