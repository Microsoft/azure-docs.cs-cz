---
title: Přesun síťových prostředků Azure do nového předplatného nebo skupiny prostředků
description: Pomocí Azure Resource Manager můžete přesunout virtuální sítě a další síťové prostředky do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: b7aaf01b696b13136a0f4077f315b137c8917906
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120125"
---
# <a name="move-guidance-for-networking-resources"></a>Pokyny pro přesun síťových prostředků

Tento článek popisuje, jak přesunout virtuální sítě a další síťové prostředky pro konkrétní scénáře.

## <a name="dependent-resources"></a>Závislé prostředky

> [!NOTE]
> Upozorňujeme, že brány VPN přidružené k veřejným IP adresám nejsou v současné době schopné přesouvat mezi skupinami prostředků nebo předplatnými.

Při přesunu prostředku je nutné také přesunout závislé prostředky (například veřejné IP adresy, brány virtuální sítě, všechny přidružené prostředky připojení). Brány místní sítě můžou být v jiné skupině prostředků.

Pokud chcete přesunout virtuální počítač s kartou síťového rozhraní do nového předplatného, musíte přesunout všechny závislé prostředky. Přesuňte virtuální síť pro síťovou kartu, všechny ostatní síťové karty virtuální sítě a brány VPN.

Další informace najdete v tématu [scénář pro přesun mezi předplatnými](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Virtuální síť s partnerským vztahem

Pokud chcete přesunout partnerský virtuální síť, musíte nejdřív zakázat partnerský vztah virtuální sítě. Jakmile je tato možnost zakázaná, můžete virtuální síť přesunout. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

## <a name="subnet-links"></a>Propojení podsítí

Virtuální síť nejde přesunout do jiného předplatného, pokud virtuální síť obsahuje podsíť s navigačními odkazy prostředků. Pokud je například prostředek Azure cache for Redis nasazený do podsítě, má tato podsíť navigační odkaz na prostředek.

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
