---
title: 'Scénář: izolace virtuální sítě'
titleSuffix: Azure Virtual WAN
description: Scénáře pro směrování – izolaci virtuální sítě
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568397"
---
# <a name="scenario-isolating-vnets"></a>Scénář: izolace virtuální sítě

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři je cílem zabránit tomu, aby virtuální sítě bylo možné oslovit jiné. Označuje se jako izolování virtuální sítě. Zatížení v rámci virtuální sítě zůstává izolované a není schopné komunikovat s jinými virtuální sítě, jako v případě jakéhokoli scénáře. Virtuální sítě ale vyžaduje, aby se dosáhlo všech větví (VPN, ER a User VPN). V tomto scénáři jsou všechna připojení VPN, ExpressRoute a User VPN přidružená ke stejné a jedné směrovací tabulce. Všechna připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek. Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Pracovní postup scénáře

Chcete-li nakonfigurovat tento scénář, proveďte následující kroky:

1. Vytvořte vlastní směrovací tabulku. V příkladu je tabulka směrování **RT_VNet**. Chcete-li vytvořit směrovací tabulku, přečtěte si téma [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md). Další informace o směrovacích tabulkách najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
2. Při vytváření směrovací tabulky **RT_VNet** nakonfigurujte následující nastavení:

   * **Asociace**: vyberte virtuální sítě, který chcete izolovat.
   * **Šíření**: vyberte možnost pro větve, což znamená, že připojení k síti (VPN/ER/P2S) budou šířit trasy do této směrovací tabulky.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Izolované virtuální sítě":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
