---
title: Azure Virtual Networks prostředky přesunout do nové předplatné nebo skupinu prostředků | Dokumentace Microsoftu
description: Použití Azure Resource Manageru pro přesun virtuálních sítí do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723490"
---
# <a name="move-guidance-for-virtual-networks"></a>Pokyny k přesunutí virtuálních sítí

Tento článek popisuje postup přesunutí virtuálních sítí pro konkrétní scénáře.

## <a name="dependent-resources"></a>Závislé prostředky

Při přesunu virtuální síť, musíte také přesunout její závislé prostředky. Pro brány sítě VPN musíte přesunout IP adresy brány virtuální sítě a všechny prostředky přidružené připojení. Brány místní sítě může být v jiné skupině prostředků.

K přesunutí virtuálního počítače pomocí karty síťového rozhraní, musíte přesunout všechny závislé prostředky. Přesuňte virtuální sítě pro síťové karty, všechny ostatní karty síťového rozhraní pro virtuální sítě a brány VPN.

## <a name="peered-virtual-network"></a>Partnerské virtuální síti

Pokud chcete přesunout partnerské virtuální síti, musíte nejprve zakázat, partnerský vztah virtuální sítě. Jakmile zakázaná, můžete přesunout virtuální sítě. Po přesunutí znovu povolte partnerský vztah virtuální sítě.

## <a name="subnet-links"></a>Odkazy na podsítě

Virtuální síť nelze přesunout do jiného předplatného, pokud virtuální síť obsahuje podsítě pomocí navigačních odkazů. Například pokud Azure pro prostředek Redis Cache se nasazuje do podsítě, má této podsíti navigační odkaz prostředku.

## <a name="next-steps"></a>Další postup

Příkazy pro přesun prostředky, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
