---
title: Přesun síťových prostředků Azure do nového předplatného nebo skupiny prostředků | Microsoft Docs
description: Pomocí Azure Resource Manager můžete přesunout virtuální sítě a další síťové prostředky do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: tomfitz
ms.openlocfilehash: 14a7cb326a3017b1bdbcad21c8483eaaacd54674
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72437807"
---
# <a name="move-guidance-for-networking-resources"></a>Pokyny pro přesun síťových prostředků

Tento článek popisuje, jak přesunout virtuální sítě a další síťové prostředky pro konkrétní scénáře.

## <a name="dependent-resources"></a>Závislé prostředky

Při přesunu virtuální sítě je nutné také přesunout závislé prostředky. U bran VPN musíte přesunout IP adresy, brány virtuální sítě a všechny přidružené prostředky připojení. Brány místní sítě můžou být v jiné skupině prostředků.

Pokud chcete přesunout virtuální počítač s kartou síťového rozhraní do nového předplatného, musíte přesunout všechny závislé prostředky. Přesuňte virtuální síť pro síťovou kartu, všechny ostatní síťové karty virtuální sítě a brány VPN.

Další informace najdete v tématu [scénář pro přesun mezi předplatnými](../resource-group-move-resources.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Virtuální síť s partnerským vztahem

Pokud chcete přesunout partnerský virtuální síť, musíte nejdřív zakázat partnerský vztah virtuální sítě. Jakmile je tato možnost zakázaná, můžete virtuální síť přesunout. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

## <a name="subnet-links"></a>Propojení podsítí

Virtuální síť nejde přesunout do jiného předplatného, pokud virtuální síť obsahuje podsíť s navigačními odkazy prostředků. Pokud je například prostředek Azure cache for Redis nasazený do podsítě, má tato podsíť navigační odkaz na prostředek.

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
