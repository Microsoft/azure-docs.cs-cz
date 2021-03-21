---
title: Informace o směrováních virtuálních center
titleSuffix: Azure Virtual WAN
description: Tento článek popisuje směrování virtuálního rozbočovače
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c1a2a54bf2d4c5de3e6cfca66256f60592fc1f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737507"
---
# <a name="about-virtual-hub-routing"></a>Informace o směrováních virtuálních center

Možnosti směrování ve virtuálním rozbočovači poskytuje směrovač, který spravuje všechna směrování mezi branami pomocí Border Gateway Protocol (BGP). Virtuální rozbočovač může obsahovat několik bran, jako je třeba Brána VPN typu Site-to-site, brána ExpressRoute, brána typu Point-to-site, Azure Firewall. Tento směrovač taky zajišťuje přenosové propojení mezi virtuálními sítěmi, které se připojují k virtuálnímu rozbočovači, a může podporovat až agregovanou propustnost 50 GB/s. Tyto možnosti směrování platí pro standardní virtuální zákazníky sítě WAN.

Pokud chcete nakonfigurovat směrování, přečtěte si téma [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Koncepty směrování

V následujících částech najdete popis klíčových konceptů v směrování virtuálního rozbočovače.

### <a name="hub-route-table"></a><a name="hub-route"></a>Tabulka směrování centra

Tabulka směrování virtuálního rozbočovače může obsahovat jednu nebo více tras. Trasa zahrnuje její název, popisek, cílový typ, seznam cílových předpon a další informace o směrování pro paket, který má být směrován. **Připojení** obvykle bude mít konfiguraci směrování přidruženou nebo rozšířenou do směrovací tabulky.

### <a name="connections"></a><a name="connection"></a>Připojení

Připojení jsou Správce prostředků prostředky, které mají konfiguraci směrování. Existují čtyři typy připojení:

* **Připojení VPN**: připojí síť VPN k bráně VPN virtuálního rozbočovače.
* **Připojení ExpressRoute**: připojuje okruh ExpressRoute k bráně ExpressRoute virtuálního rozbočovače.
* **Připojení konfigurace P2S**: připojí konfiguraci VPN uživatele (Point-to-site) k bráně virtuální sítě VPN (Point-to-site) uživatele virtuálního rozbočovače.
* **Připojení k virtuální síti rozbočovače**: propojuje virtuální sítě s virtuálním rozbočovačem.

Během instalace můžete nastavit konfiguraci směrování pro připojení k virtuální síti. Ve výchozím nastavení se všechna připojení přiřadí a šíří do výchozí směrovací tabulky.

### <a name="association"></a><a name="association"></a>Řídí

Každé připojení je přidruženo k jedné směrovací tabulce. Přidružením připojení k tabulce směrování lze provoz odeslat do cíle uvedeného jako trasy v tabulce směrování. Konfigurace směrování připojení zobrazí přidruženou směrovací tabulku.  Ke stejné směrovací tabulce může být přidruženo více připojení. Všechna připojení VPN, ExpressRoute a User VPN jsou přidružena ke stejné (výchozí) směrovací tabulce.

Ve výchozím nastavení jsou všechna připojení přidružena k **výchozí směrovací tabulce** ve virtuálním rozbočovači. Každé virtuální centrum má svou vlastní výchozí směrovací tabulku, kterou je možné upravit pro přidání statických tras. Trasy přidané staticky mají přednost před dynamicky zjištěnými trasami pro stejné předpony.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Řídí":::

### <a name="propagation"></a><a name="propagation"></a>Šíření

Připojení dynamicky šíří trasy do směrovací tabulky. S připojením VPN, připojením ExpressRoute nebo připojením konfigurace P2S se trasy šíří z virtuálního rozbočovače na místní směrovač pomocí protokolu BGP. Trasy lze rozšířit do jedné nebo více směrovacích tabulek.

**Tabulka směrování None** je k dispozici také pro každé virtuální centrum. Rozšiřování do tabulky směrování None znamená, že pro rozšíření od připojení nejsou nutné žádné trasy. Připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Šíření":::

### <a name="labels"></a><a name="labels"></a>Popisky

Popisky poskytují mechanismus pro logickou skupinu směrovacích tabulek. To je užitečné hlavně při šíření tras z připojení k několika směrovacím tabulkám. Například **výchozí směrovací tabulka** má vestavěný popisek s názvem default. Když uživatelé šíří trasy připojení k výchozímu popisku, bude automaticky platit pro všechny výchozí směrovací tabulky napříč všemi rozbočovači ve virtuální síti WAN.

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Konfigurace statických tras v připojení k virtuální síti

Konfigurace statických tras poskytuje mechanismus pro řízení provozu prostřednictvím IP adresy dalšího směrování, což může být virtuální síťové zařízení (síťové virtuální zařízení) zřízené ve virtuální síti paprsků připojené k virtuálnímu rozbočovači. Statická trasa se skládá z názvu trasy, seznamu předpon cíle a IP adresy dalšího směrování.

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>Směrovací tabulky pro předem existující trasy

Směrovací tabulky teď mají funkce pro přidružení a šíření. Již existující směrovací tabulka je směrovací tabulka, která nemá tyto funkce. Pokud máte již existující trasy ve směrování centra a chcete používat nové funkce, vezměte v úvahu následující skutečnosti:

* **Standardní virtuální zákazníci sítě WAN s předem existujícími trasami ve virtuálním centru**:

   Pokud máte již existující trasy v části směrování pro centrum v Azure Portal, bude nutné je nejprve odstranit a pak se pokusit vytvořit nové směrovací tabulky (k dispozici v části směrovací tabulky pro centrum v Azure Portal).

* **Základní virtuální zákazníci sítě WAN s předem existujícími trasami ve virtuálním centru**:

   Pokud máte již existující trasy v části směrování pro centrum v Azure Portal, budete je muset nejdřív odstranit a pak **upgradovat** základní virtuální síť WAN na standard Virtual WAN. Viz [upgrade virtuální sítě WAN z úrovně Basic na standard](upgrade-virtual-wan.md).

## <a name="hub-reset"></a><a name="reset"></a>Resetování centra

**Resetování** virtuálního rozbočovače je k dispozici pouze v Azure Portal. Resetování nabízí způsob, jak přenést všechny neúspěšné prostředky, jako jsou směrovací tabulky, směrovač rozbočovače nebo prostředek virtuálního rozbočovače, zpátky do svého stavu zřizování rightful. Než se obrátíte na podporu Microsoftu, zvažte nastavení centra. Tato operace neresetuje žádné brány ve virtuálním rozbočovači.

## <a name="additional-considerations"></a><a name="considerations"></a>Další aspekty

Při konfiguraci směrování virtuální sítě WAN Vezměte v úvahu následující skutečnosti:

* Všechna připojení větví (Point-to-site, Site-to-site a ExpressRoute) musí být přidružena k výchozí směrovací tabulce. V takovém případě se všechny větve budou učit stejné předpony.
* Všechna připojení větví potřebují rozšířit své trasy do stejné sady směrovacích tabulek. Pokud se například rozhodnete, že by se větve měly rozšířit do výchozí směrovací tabulky, tato konfigurace by měla být konzistentní napříč všemi větvemi. V důsledku toho budou mít všechna připojení přidružená k výchozí směrovací tabulce přístup k všem větvím.
* Větev-do větve prostřednictvím Azure Firewall není aktuálně podporována.
* Při použití Azure Firewall ve více oblastech musí být všechny virtuální sítě s paprsky přidruženy ke stejné směrovací tabulce. Například podmnožina virtuální sítě projde skrze Azure Firewall, zatímco jiné virtuální sítěy obcházejí Azure Firewall ve stejném virtuálním rozbočovači není možné.
* Pro každé připojení k virtuální síti se dá nakonfigurovat jedna IP adresa dalšího směrování.

## <a name="next-steps"></a>Další kroky

* Pokud chcete nakonfigurovat směrování, přečtěte si téma [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md).
* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
