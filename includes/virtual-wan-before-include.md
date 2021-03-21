---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359492"
---
* Máte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Chcete-li vytvořit virtuální síť v Azure Portal, přečtěte si článek [rychlý Start](../articles/virtual-network/quick-create-portal.md) .

* Vaše virtuální síť nesmí mít žádné existující brány virtuální sítě. Pokud už vaše virtuální síť obsahuje brány (VPN nebo ExpressRoute), musíte před tím, než budete pokračovat, odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě připojovaly jenom k bráně virtuální sítě WAN.

* Virtuální rozbočovač je virtuální síť, kterou vytváří a používá virtuální síť WAN. Je základem vaší virtuální sítě WAN v určité oblasti. Získejte rozsah IP adres pro vaši oblast virtuálního centra. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Také se nemůže překrývat s rozsahy místních adres, ke kterým se připojujete. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.
