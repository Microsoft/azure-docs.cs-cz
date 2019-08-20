---
title: Přesun síťových prostředků Azure do nového předplatného nebo skupiny prostředků | Microsoft Docs
description: Pomocí Azure Resource Manager můžete přesunout virtuální sítě a další síťové prostředky do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626259"
---
# <a name="move-guidance-for-networking-resources"></a>Pokyny pro přesun síťových prostředků

Tento článek popisuje, jak přesunout virtuální sítě a další síťové prostředky pro konkrétní scénáře.

## <a name="dependent-resources"></a>Závislé prostředky

Při přesunu virtuální síť, musíte také přesunout její závislé prostředky. Pro brány sítě VPN musíte přesunout IP adresy brány virtuální sítě a všechny prostředky přidružené připojení. Brány místní sítě může být v jiné skupině prostředků.

Chcete-li přesunout virtuální počítač pomocí síťové karty, je nutné přesunout všechny závislé prostředky. Přesuňte virtuální síť pro síťovou kartu, všechny ostatní síťové karty virtuální sítě a brány VPN.

## <a name="state-of-dependent-resources"></a>Stav závislých prostředků

Pokud zdrojová nebo cílová skupina prostředků obsahuje virtuální síť, při přesunu se zkontrolují stavy všech závislých prostředků pro virtuální síť. Pokud některý z těchto prostředků je ve stavu selhání, přesun se zablokuje. Pokud třeba virtuální počítač, který používá virtuální síť, selhal, přesun se zablokuje. Přesunutí je blokováno i v případě, že virtuální počítač není jedním z přesouvaných prostředků a není v jedné ze skupin prostředků pro přesunutí. Pokud se chcete tomuto problému vyhnout, přesuňte prostředky do skupiny prostředků, která nemá virtuální síť.

## <a name="peered-virtual-network"></a>Virtuální síť s partnerským vztahem

Pokud chcete přesunout partnerské virtuální síti, musíte nejprve zakázat, partnerský vztah virtuální sítě. Jakmile zakázaná, můžete přesunout virtuální sítě. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

## <a name="subnet-links"></a>Propojení podsítí

Virtuální síť nelze přesunout do jiného předplatného, pokud virtuální síť obsahuje podsítě pomocí navigačních odkazů. Například pokud Azure pro prostředek Redis Cache se nasazuje do podsítě, má této podsíti navigační odkaz prostředku.

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo](../resource-group-move-resources.md)předplatného.
