---
title: 'Práce na dálku pomocí bašty: Azure Bastion'
description: Tato stránka popisuje, jak můžete využít Azure Bastion k povolení práce na dálku kvůli pandemii COVID-19.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: c0bba2560cccc208e2384421218ecebdfef65236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333068"
---
# <a name="working-remotely-using-azure-bastion"></a>Vzdálená práce s Azure Bastion

Azure Bastion hraje klíčovou roli při podpoře vzdálených pracovních scénářů tím, že umožňuje uživatelům s připojením k internetu přístup k virtuálním počítačům Azure. Zejména umožňuje správcům IT spravovat své aplikace spuštěné v Azure kdykoli a odkudkoli v celém světě.

>[!NOTE]
>Tento článek popisuje, jak můžete využít Azure Bastion, Azure, microsoft network a ekosystém partnerů Azure k vzdálené práci a zmírnění problémů se sítí, kterým čelíte kvůli krizi COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Bezpečný přístup k virtuálním počítačům

Konkrétně Azure Bastion poskytuje zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům v rámci virtuální sítě Azure, přímo na portálu Azure, bez použití veřejné IP adresy. Další informace o architektuře Azure Bastion a klíčových funkcích najdete v tématu [Co je Azure Bastion](bastion-overview.md).

Azure Bastion se nasadí na virtuální síť, což znamená, že společnosti můžou nakonfigurovat a spravovat jednu baštu Azure, aby rychle podporovaly vzdálený uživatelský přístup k virtuálním počítačům v rámci virtuální sítě Azure. Pokyny k vytvoření a správě Azure Bastion najdete v najdete v roce [vytvoření hostitele bašty](bastion-create-host-portal.md).

## <a name="next-steps"></a>Další kroky

* Konfigurace Azure Bastion pomocí [portálu Azure](bastion-create-host-portal.md), [PowerShellu](bastion-create-host-powershell.md)nebo rozhraní příkazového příkazu Konazure.

* Další informace našlápejte na časté dotazy k [baště.](bastion-faq.md)
