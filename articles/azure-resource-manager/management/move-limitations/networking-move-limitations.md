---
title: Přesunutí síťových prostředků Azure do nového předplatného nebo skupiny prostředků
description: Pomocí Správce prostředků Azure přesuňte virtuální sítě a další síťové prostředky do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485231"
---
# <a name="move-guidance-for-networking-resources"></a>Přesunout pokyny pro síťové prostředky

Tento článek popisuje, jak přesunout virtuální sítě a další síťové prostředky pro konkrétní scénáře.

## <a name="dependent-resources"></a>Závislé prostředky

Při přesouvání virtuální sítě je nutné také přesunout její závislé prostředky. U bran VPN je nutné přesunout IP adresy, brány virtuální sítě a všechny přidružené prostředky připojení. Brány místní sítě mohou být v jiné skupině prostředků.

Chcete-li přesunout virtuální počítač s kartou síťového rozhraní do nového předplatného, musíte přesunout všechny závislé prostředky. Přesuňte virtuální síť pro kartu síťového rozhraní, všechny ostatní karty síťového rozhraní pro virtuální síť a brány VPN.

Další informace naleznete v [tématu Scénář pro přesun mezi předplatnými](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Partnerská virtuální síť

Chcete-li přesunout partnerské virtuální sítě, musíte nejprve zakázat partnerský vztah virtuální sítě. Po zakázání můžete virtuální síť přesunout. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

## <a name="subnet-links"></a>Odkazy podsítí

Virtuální síť nelze přesunout do jiného předplatného, pokud virtuální síť obsahuje podsíť s odkazy na navigaci prostředků. Například pokud azure cache pro Redis prostředek je nasazen do podsítě, tato podsíť má navigační odkaz na prostředek.

## <a name="next-steps"></a>Další kroky

Příkazy k přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
