---
title: 'Vzdálená práce pomocí bastionu: Azure bastionu'
description: Tato stránka popisuje, jak můžete využít Azure bastionu k vzdálené práci z důvodu COVID-19 PANDEMIC.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077945"
---
# <a name="working-remotely-using-azure-bastion"></a>Vzdálená práce pomocí Azure bastionu

Azure bastionu hraje v podpoře vzdálených pracovních scénářů pivotovou roli tím, že umožňuje uživatelům s připojením k Internetu přístup k virtuálním počítačům Azure. Zejména umožňuje správcům IT spravovat aplikace běžící v Azure kdykoli a odkudkoli po celém světě.

>[!NOTE]
>Tento článek popisuje, jak můžete využít Azure bastionu, Azure, síť Microsoftu a partnerský ekosystém Azure k tomu, abyste mohli vzdáleně pracovat a zmírnit problémy se sítí, na které se chystáte z důvodu krize v COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Zabezpečený přístup k virtuálním počítačům

Konkrétně Azure bastionu poskytuje zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům v rámci virtuální sítě Azure přímo v Azure Portal bez použití veřejné IP adresy. Další informace o architektuře a klíčových funkcích Azure bastionu najdete v části [co je Azure bastionu](bastion-overview.md).

Azure bastionu se nasazuje na virtuální síť, což znamená, že společnosti můžou nakonfigurovat a spravovat jednu službu Azure bastionu a rychle tak podporovat vzdálený přístup uživatelů k virtuálním počítačům v rámci virtuální sítě Azure. Pokyny k vytvoření a správě Azure bastionu najdete v tématu [Vytvoření hostitele bastionu](./tutorial-create-host-portal.md).

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte Azure bastionu pomocí [Azure Portal](./tutorial-create-host-portal.md), [PowerShellu](bastion-create-host-powershell.md)nebo rozhraní příkazového řádku Azure.

* Další informace najdete v tématu [bastionu – Nejčastější dotazy](bastion-faq.md) .