---
title: 'Scénář: směrování provozu přes síťové virtuální zařízení'
titleSuffix: Azure Virtual WAN
description: Směrování provozu přes síťové virtuální zařízení
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142977"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scénář: směrování provozu přes síťové virtuální zařízení

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři síťové virtuální zařízení je cílem směrovat provoz prostřednictvím síťové virtuální zařízení (síťového virtuálního zařízení) pro větev do virtuální sítě a virtuální sítě do větve. Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

> [!NOTE]
> Některé možnosti směrování se pořád můžou vyvádět. Pokud ve vaší oblasti ještě nedošlo k zavedení, použijte prosím následující postup v těchto verzích těchto článků:
>* [Azure Portal článek](virtual-wan-route-table-nva-portal.md)
>* [Článek o PowerShellu](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scénáře

Na **obrázku 1**existují dvě centra; **Centrum 1** a **hub 2**.

* **Centrum 1** a **rozbočovač 2** jsou přímo připojené k síťové virtuální zařízení virtuální sítě **VNet 2** a **VNet 4**.

* **Virtuální síť 5** a **virtuální síť 6** jsou v partnerském vztahu s **virtuální sítí 2**.

* **Virtuální síť 7** a **virtuální síť 8** jsou v partnerském vztahu s **virtuální sítí 4**.

* **Virtuální sítě 5, 6, 7, 8** jsou nepřímé paprsky, ne přímo připojené k virtuálnímu rozbočovači.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Obrázek 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Pracovní postup scénáře

Pokud chcete nastavit směrování přes síťové virtuální zařízení, tady je postup, který je potřeba vzít v úvahu:

1. Identifikujte připojení virtuální sítě síťové virtuální zařízení paprsku. Na **obrázku 1**jsou **připojení VNet 2 (Eastusconn)** a připojení k **virtuální síti 4 (weconn)**.

   Zajistěte, aby byly nastavené udr:
   * Z virtuální sítě 5 a 6 na virtuální síť 2 síťové virtuální zařízení IP adresu
   * Z virtuální sítě 7 a 8 na virtuální síť 4 síťové virtuální zařízení IP adresu 
   
   Virtuální síť 5, 6, 7, 8 nemusíte připojovat přímo k virtuálním rozbočovačům. Ujistěte se, že skupin zabezpečení sítě v virtuální sítě 5, 6, 7, 8 povoluje provoz pro větev (VPN/ER/P2S) nebo virtuální sítě připojená ke svému vzdálenému virtuální sítě. Například virtuální síť 5, 6 musí zajistit, aby skupin zabezpečení sítě povolovaly přenosy místních předpon adres a virtuální sítě 7, 8, které jsou připojené ke vzdálenému rozbočovači 2. 

2. Přidejte agregovanou položku statické trasy pro virtuální sítě 2, 5, 6 do výchozí směrovací tabulky centra 1. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Příklad":::

3. Nakonfigurujte statickou trasu pro virtuální sítě 5, 6 ve virtuální síti síťového připojení 2. Informace o nastavení konfigurace směrování pro připojení k virtuální síti najdete v tématu [Směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md#routing-configuration).

4. Přidejte agregovanou položku statického směrování pro výchozí směrovací tabulku virtuální sítě 4, 7, 8 do středu 1.

5. Opakujte kroky 2, 3 a 4 pro výchozí směrovací tabulku centra 2.

Výsledkem bude, že se změní konfigurace směrování, jak je vidět na následujícím obrázku.

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Výsledek":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
