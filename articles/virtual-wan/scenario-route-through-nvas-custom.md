---
title: 'Scénář: směrování provozu prostřednictvím síťová virtuální zařízení pomocí vlastního nastavení'
titleSuffix: Azure Virtual WAN
description: Tento scénář vám pomůže směrovat provoz prostřednictvím síťová virtuální zařízení pomocí různých síťové virtuální zařízení pro přenosy vázané na Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568470"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scénář: směrování provozu prostřednictvím síťová virtuální zařízení-Custom (Preview)

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři síťové virtuální zařízení (síťové virtuální zařízení) je cílem směrovat provoz přes síťové virtuální zařízení pro virtuální síť VNet <-> a použít jiný síťové virtuální zařízení pro přenosy vázané na Internet. Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scénáře

Na **obrázku 1**je jeden rozbočovač, **centrum 1**.

* **Centrum 1** je přímo připojené k síťové virtuální zařízení virtuální sítě **VNet 4** a **VNet 5**.

* U provozu mezi virtuální sítě 1, 2 a 3 a větvemi (VPN/ER/P2S) se očekává, že se bude přecházet přes **virtuální síť 4 síťové virtuální zařízení** 10.4.0.5.

* Každý internetový provoz vázaný z virtuální sítě 1, 2 a 3 se očekává přes **virtuální síť 5 síťové virtuální zařízení** 10.5.0.5.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Obrázek 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Pracovní postup scénáře

Pokud chcete nastavit směrování přes síťové virtuální zařízení, tady je postup, který je potřeba vzít v úvahu:

1. Aby přenosy dat vázané na Internet procházejí přes síti VNET5, potřebujete virtuální sítě 1, 2 a 3 pro přímé připojení prostřednictvím partnerského vztahu virtuálních sítí k virtuální síti 5. Budete také potřebovat UDR sadu v virtuální sítě pro 0.0.0.0/0 a další segment směrování 10.5.0.5. Virtuální síť WAN v současné době neumožňuje síťové virtuální zařízení dalšího směrování ve virtuálním rozbočovači pro 0.0.0.0/0.

1. V Azure Portal přejděte do svého virtuálního centra a vytvořte vlastní směrovací tabulku **RT_Shared** , která se bude naučit trasy prostřednictvím šíření ze všech připojení virtuální sítě a větví. Na **obrázku 2**je znázorněno jako prázdná vlastní směrovací tabulka **RT_Shared**.

   * **Trasy:** Nemusíte přidávat žádné statické trasy.

   * **Přidružení:** Vyberte virtuální sítě 4 a 5, což znamená, že připojení virtuální sítě 4 a 5 se přidruží ke směrovací tabulce **RT_Shared**.

   * **Šíření:** Vzhledem k tomu, že chcete, aby všechny větve a připojení virtuální sítě dynamicky rozšířily své trasy do této směrovací tabulky, vyberte možnost větve a všechny virtuální sítě.

1. Vytvořte vlastní směrovací tabulku **RT_V2B** pro směrování provozu z virtuální sítě 1, 2 a 3 do větví.

   * **Trasy:** Přidat agregovanou položku statického směrování pro větve (VPN/ER/P2S) (10.2.0.0/16 na **obrázku 2**) s dalším segmentem směrování jako připojení virtuální sítě 4. Také je potřeba nakonfigurovat statickou trasu v připojení virtuální sítě 4 pro předpony větví a označit další směrování jako konkrétní IP adresu síťové virtuální zařízení ve virtuální síti 4.

   * **Přidružení:** Vyberte všechny virtuální sítě 1, 2 a 3. To znamená, že připojení k virtuální síti 1, 2 a 3 se přidruží k této tabulce směrování a budou schopna učit se trasy (statické a dynamické prostřednictvím šíření) v této směrovací tabulce.

   * **Šíření:** Připojení šíří trasy do směrovacích tabulek. Když vyberete virtuální sítě 1, 2 a 3, umožníte šíření tras z virtuální sítě 1, 2 a 3 do této směrovací tabulky. Nemusíte šířit trasy z připojení větví do RT_V2B, protože provoz virtuální sítě VNet prochází přes síťové virtuální zařízení v VNET4.
  
1. Upravte výchozí směrovací tabulku **DefaultRouteTable**.

   Všechna připojení VPN, ExpressRoute a User VPN jsou přidružena k výchozí směrovací tabulce. Všechna připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek.

   * **Trasy:** Přidejte agregovanou položku statické trasy pro virtuální sítě 1, 2 a 3 (10.1.0.0/16 na **obrázku 2**) s dalším segmentem směrování jako připojení virtuální sítě 4. Pro virtuální síť 1, 2 a 3 agregované předpony musíte také nakonfigurovat statickou trasu v připojení virtuální sítě 4 a označit další směrování jako konkrétní IP adresu síťové virtuální zařízení ve virtuální síti 4.

   * **Přidružení:** Ujistěte se, že je vybraná možnost pro větve (VPN/ER/P2S), což zajišťuje, že připojení místních větví k *defaultroutetable*se přidružit.

   * **Šíření z:** Ujistěte se, že je vybraná možnost pro větve (VPN/ER/P2S), což zajistí, že místní připojení šíří trasy do *defaultroutetable*.

**Obrázek 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Obrázek 2":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
