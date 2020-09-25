---
title: Sítě a konektivita řešení Azure VMWare
description: Popis připojení a sítě řešení Azure VMWare.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 6c45043ebbbc5099f512a1d746e48d2a8a065e6f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316884"
---
<!-- Used in introduction.md and concepts-networking.md -->

Řešení Azure VMware nabízí prostředí privátního cloudu, které je přístupné z místních prostředí a prostředí založeného na Azure nebo prostředků. Připojení poskytují služby, jako je Azure ExpressRoute a VPN. Tyto služby vyžadují pro povolení služeb konkrétní rozsahy síťových adres a porty brány firewall.

Při nasazení privátního cloudu se vytvoří privátní sítě pro správu, zřizování a vMotion. Tyto privátní sítě použijte pro přístup k vCenter a NSX Manageru a vMotion nebo nasazení virtuálních počítačů.  ExpressRoute Global Reach slouží k propojení privátních cloudů s místními prostředími. Připojení vyžaduje virtuální síť s okruhem ExpressRoute v rámci vašeho předplatného.


>[!NOTE]
>Přístup k Internetu a službám Azure se zřizuje a poskytuje pro využívání virtuálních počítačů v produkčních sítích při nasazení privátního cloudu.  Ve výchozím nastavení je přístup k Internetu zakázán pro nové privátní cloudy a kdykoli může být povolen nebo zakázán.