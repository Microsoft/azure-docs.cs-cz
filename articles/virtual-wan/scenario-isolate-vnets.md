---
title: 'Scénář: izolace virtuální sítě'
titleSuffix: Azure Virtual WAN
description: Scénáře pro směrování – izolaci virtuální sítě
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92424747"
---
# <a name="scenario-isolating-vnets"></a>Scénář: izolace virtuální sítě

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři je cílem zabránit tomu, aby virtuální sítě bylo možné oslovit jiné. Označuje se jako izolování virtuální sítě. Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Návrh

V tomto scénáři zůstane úloha v rámci určité virtuální sítě izolovaná a nebude schopná komunikovat s jinými virtuální sítě. Virtuální sítě ale vyžaduje, aby se dosáhlo všech větví (VPN, ER a User VPN). Aby bylo možné zjistit, kolik směrovacích tabulek bude potřeba, můžete vytvořit matici připojení. V tomto scénáři bude vypadat podobně jako v následující tabulce, kde každá buňka představuje, zda zdroj (řádek) může komunikovat s cílem (sloupec):

| Z |   Záměr |  *Virtuální sítě* | *Větve* |
| -------------- | -------- | ---------- | ---|
| Virtuální sítě     | &#8594;| Direct |   Direct    |
| Větve   | &#8594;|  Direct  |   Direct    |

Každá z buněk v předchozí tabulce popisuje, jestli připojení k virtuální síti WAN (strana "od" na straně toku, záhlaví řádků) komunikuje s předponou cíle (stranu "do" toku, záhlaví sloupce kurzívou). V tomto scénáři nejsou k dispozici žádné brány firewall ani síťová virtuální zařízení, takže komunikace směřuje přímo přes virtuální síť WAN (takže slovo "Direct" v tabulce).

Tato matice připojení poskytuje dva různé vzory řádků, které se převádějí do dvou směrovacích tabulek. Virtuální síť WAN už má výchozí směrovací tabulku, takže budeme potřebovat jinou směrovací tabulku. V tomto příkladu budeme pojmenovat směrovací tabulku **RT_VNET**.

Virtuální sítě bude přidružen k této směrovací tabulce **RT_VNET** . Vzhledem k tomu, že potřebují připojení ke větvím, větve bude nutné rozšířit na **RT_VNET** (jinak by se virtuální sítěy nedozvěděly předpony větve). Vzhledem k tomu, že větve jsou vždy přidruženy k výchozí směrovací tabulce, virtuální sítě bude nutné rozšířit do výchozí směrovací tabulky. V důsledku toho je to konečný návrh:

* Virtuální sítě:
  * Přidružená směrovací tabulka: **RT_VNET**
  * Rozšiřování do směrovacích tabulek: **výchozí**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **RT_VNET** a **výchozí**

Všimněte si, že vzhledem k tomu, že se jenom větve šíří do směrovací tabulky **RT_VNET**, budou se jednat o jediné předpony, které se virtuální sítě, a ne jiné virtuální sítě.

Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Chcete-li nakonfigurovat tento scénář, proveďte následující kroky:

1. Vytvořte vlastní směrovací tabulku v každém centru. V příkladu je tabulka směrování **RT_VNet**. Chcete-li vytvořit směrovací tabulku, přečtěte si téma [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md). Další informace o směrovacích tabulkách najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
2. Při vytváření směrovací tabulky **RT_VNet** nakonfigurujte následující nastavení:

   * **Asociace**: vyberte virtuální sítě, který chcete izolovat.
   * **Šíření**: vyberte možnost pro větve, což znamená, že připojení k síti (VPN/ER/P2S) budou šířit trasy do této směrovací tabulky.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Izolované virtuální sítě":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
