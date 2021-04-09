---
title: Postup konfigurace směrování virtuálního centra
titleSuffix: Azure Virtual WAN
description: Tento článek popisuje, jak nakonfigurovat směrování virtuálního rozbočovače.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313684"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Postup konfigurace směrování virtuálního centra

Virtuální rozbočovač může obsahovat několik bran, například bránu VPN typu Site-to-site, bránu ExpressRoute, bránu Point-to-site a Azure Firewall. Možnosti směrování ve virtuálním centru poskytuje směrovač, který spravuje všechny směrování, včetně směrování mezi bránami pomocí Border Gateway Protocol (BGP). Tento směrovač taky zajišťuje přenosové propojení mezi virtuálními sítěmi, které se připojují k virtuálnímu rozbočovači, a může podporovat až agregovanou propustnost 50 GB/s. Tyto možnosti směrování platí pro standardní virtuální zákazníky sítě WAN.

Další informace najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="create-a-route-table"></a><a name="create-table"></a>Vytvoření směrovací tabulky

1. V Azure Portal přejděte do virtuálního centra.
2. V části **připojení** vyberte **Směrování**. Na stránce směrování se zobrazí **výchozí** a **žádné** směrovací tabulky.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Stránka směrování":::
3. Výběrem **+ vytvořit směrovací tabulku** otevřete stránku **vytvořit tabulku směrování** .
4. Na kartě **základy** stránky vytvořit směrovací tabulku vyplňte následující pole.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Karta základy":::

   * **Název**
   * **Trasy**
   * **Název trasy**
   * **Cílový typ**
   * **Předpona cíle**: můžete agregovat předpony. Příklad: virtuální síť 1:10.1.0.0/24 a virtuální síť 2:10.1.1.0/24 se dají agregovat jako 10.1.0.0/16. Trasy **větví** se vztahují na všechny připojené weby sítě VPN, okruhy ExpressRoute a uživatelská připojení VPN.
   * **Další směrování**: seznam připojení k virtuální síti nebo Azure firewall.

     Pokud vyberete připojení k virtuální síti, zobrazí se **Konfigurace statických tras**. Toto je volitelné nastavení konfigurace. Další informace najdete v tématu [Konfigurace statických tras](about-virtual-hub-routing.md#static).

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Další směrování":::

5. Vyberte kartu **Štítky** pro konfiguraci názvů popisků. Popisky poskytují mechanismus pro logickou skupinu směrovacích tabulek.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Konfigurace názvů popisků":::

6. Vyberte kartu **přidružení** a přidružte k tabulce směrování připojení.
Zobrazí se **větve**, **virtuální sítě** a **aktuální nastavení** připojení.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Připojení přidružení ke směrovací tabulce":::

7. Vyberte kartu **šíření** a rozšiřte trasy z připojení do směrovací tabulky.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Rozšířit trasy":::

8. Vyberte **vytvořit** a vytvořte tabulku směrování.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Úprava směrovací tabulky

V Azure Portal vyhledejte směrovací tabulku svého virtuálního rozbočovače. Vyberte směrovací tabulku pro úpravu libovolných informací.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Odstranění směrovací tabulky

V Azure Portal vyhledejte směrovací tabulku svého virtuálního rozbočovače. Nemůžete odstranit tabulku směrování default ani None. Můžete ale odstranit všechny vlastní směrovací tabulky. Klikněte na **"..."** a pak vyberte **Odstranit**.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Postup zobrazení efektivních tras

V Azure Portal vyhledejte směrovací tabulku svého virtuálního rozbočovače. Klikněte na **"..."** a vyberte **efektivní trasy** pro zobrazení tras, které získala vybraná směrovací tabulka. Šířené trasy z připojení do směrovací tabulky se automaticky naplní v **platných trasách** směrovací tabulky. Další informace najdete v tématu [o platných trasách](effective-routes-virtual-hub.md).

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Zobrazit efektivní trasy" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Nastavení konfigurace směrování pro připojení k virtuální síti

1. V Azure Portal přejděte na virtuální síť WAN a v části **připojení** vyberte **Virtual Network připojení**.
1. Vyberte **+ Přidat připojení**.
1. Z rozevíracího seznamu vyberte virtuální síť.
1. Nastavte konfiguraci směrování, která se má přidružit k směrovací tabulce. V poli **přidružená tabulka směrování** vyberte směrovací tabulku z rozevíracího seznamu.
1. Nastavte konfiguraci směrování pro rozšíření na jednu nebo více směrovacích tabulek. V poli **rozšířit do směrovací tabulky** vyberte z rozevíracího seznamu.
1. U **statických tras** nakonfigurujte statické trasy pro síťové virtuální zařízení (Pokud je k dispozici). Virtuální síť WAN podporuje jednu IP adresu dalšího směrování pro statickou trasu v připojení k virtuální síti. Pokud máte například samostatné virtuální zařízení pro toky příchozího a odchozího přenosu dat, doporučujeme, aby virtuální zařízení byla oddělená virtuální sítě a připojili virtuální sítě k virtuálnímu rozbočovači.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Nastavení konfigurace směrování" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Další kroky

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
