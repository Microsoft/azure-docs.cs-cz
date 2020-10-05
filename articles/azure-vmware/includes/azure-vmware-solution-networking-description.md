---
title: Sítě a konektivita řešení Azure VMWare
description: Popis připojení a sítě řešení Azure VMWare.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574448"
---
<!-- Used in introduction.md and concepts-networking.md -->

Řešení Azure VMware nabízí prostředí privátního cloudu, které je přístupné z místních prostředí a prostředí založeného na Azure nebo prostředků. Připojení poskytují služby, jako je Azure ExpressRoute a VPN. Tyto služby vyžadují pro povolení služeb konkrétní rozsahy síťových adres a porty brány firewall.

Při nasazení privátního cloudu se vytvoří privátní sítě pro správu, zřizování a vMotion. Tyto privátní sítě použijte pro přístup k vCenter a NSX Manageru a vMotion nebo nasazení virtuálních počítačů.  ExpressRoute Global Reach slouží k propojení privátních cloudů s místními prostředími. Připojení vyžaduje virtuální síť s okruhem ExpressRoute v rámci vašeho předplatného.



>[!NOTE]
>Přístup k Internetu a službám Azure se zřizuje a poskytuje pro využívání virtuálních počítačů v produkčních sítích při nasazení privátního cloudu.  Ve výchozím nastavení je přístup k Internetu zakázán pro nové privátní cloudy a kdykoli může být povolen nebo zakázán.