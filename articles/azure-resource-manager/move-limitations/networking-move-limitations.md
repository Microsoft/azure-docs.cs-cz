---
title: Přesun síťových prostředků Azure do nového předplatného nebo skupiny prostředků
description: Pomocí Azure Resource Manager můžete přesunout virtuální sítě a další síťové prostředky do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: c4e6acb52f6342c57fb1db9fc3e83d90d6d01285
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150822"
---
# <a name="move-guidance-for-networking-resources"></a>Pokyny pro přesun síťových prostředků

Tento článek popisuje, jak přesunout virtuální sítě a další síťové prostředky pro konkrétní scénáře.

## <a name="dependent-resources"></a>Závislé prostředky

Při přesunu virtuální síť, musíte také přesunout její závislé prostředky. Pro brány sítě VPN musíte přesunout IP adresy brány virtuální sítě a všechny prostředky přidružené připojení. Brány místní sítě může být v jiné skupině prostředků.

Pokud chcete přesunout virtuální počítač s kartou síťového rozhraní do nového předplatného, musíte přesunout všechny závislé prostředky. Přesuňte virtuální síť pro síťovou kartu, všechny ostatní síťové karty virtuální sítě a brány VPN.

Další informace najdete v tématu [scénář pro přesun mezi předplatnými](../resource-group-move-resources.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Virtuální síť s partnerským vztahem

Pokud chcete přesunout partnerské virtuální síti, musíte nejprve zakázat, partnerský vztah virtuální sítě. Jakmile zakázaná, můžete přesunout virtuální sítě. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

## <a name="subnet-links"></a>Propojení podsítí

Virtuální síť nelze přesunout do jiného předplatného, pokud virtuální síť obsahuje podsítě pomocí navigačních odkazů. Například pokud Azure pro prostředek Redis Cache se nasazuje do podsítě, má této podsíti navigační odkaz prostředku.

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
