---
title: 'Scénář: Azure Firewall vlastní směrování pro virtuální síť WAN'
titleSuffix: Azure Virtual WAN
description: Scénáře směrování provozu Směrování mezi virtuální sítě, ale použijte Azure Firewall pro síť VNet->Internet/Branch a větev do toků provozu sítě VNet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 301bc64bee291fa25506e7f435e923be7e244cd4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267512"
---
# <a name="scenario-azure-firewall---custom"></a>Scénář: Azure Firewall-vlastní

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V tomto scénáři je cílem směrovat provoz mezi virtuální sítě přímo, ale použít Azure Firewall pro toky Cloud-to-Internet/Branch a větví přenosů mezi virtuálními sítěmi.

## <a name="design"></a><a name="design"></a>Návrh

Aby bylo možné zjistit, kolik směrovacích tabulek bude potřeba, můžete vytvořit matrici připojení, kde každá buňka představuje, jestli zdroj (řádek) může komunikovat s cílem (sloupec). Matice připojení v tomto scénáři je triviální, ale je konzistentní s jinými scénáři, můžeme se přesto podívat i na to.

**Matice připojení**

| Z           | Do:      | *Virtuální sítě*      | *Větve*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **Virtuální sítě**      |   &#8594;|     X        |     AzFW      |     AzFW     |
| **Větve**   |   &#8594;|    AzFW      |       X       |       X      |

V předchozí tabulce představuje "X" přímé připojení mezi dvěma připojeními bez provozu přenášeného Azure Firewall ve virtuální síti WAN a "AzFW" znamená, že tok projde Azure Firewall. Vzhledem k tomu, že v matici existují dva odlišné vzory připojení, budeme potřebovat dvě směrovací tabulky, které budou nakonfigurovány takto:

* Virtuální sítě:
  * Přidružená směrovací tabulka: **RT_VNet**
  * Rozšiřování do směrovacích tabulek: **RT_VNet**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **výchozí**

> [!NOTE]
> V každé oblasti můžete vytvořit samostatnou virtuální instanci sítě WAN s jedním zabezpečeným virtuálním rozbočovačem a pak můžete každou virtuální síť WAN připojit k sobě mezi sebou prostřednictvím sítě Site-to-Site VPN.

Informace o směrování virtuálního rozbočovače najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Pracovní postup

V tomto scénáři chcete směrovat provoz prostřednictvím Azure Firewall pro provoz typu VNet-to-Internet, VNet-to-větvi nebo propojení mezi virtuálními sítěmi, ale chcete přejít přímo k provozu typu VNet-to-VNet. Pokud jste použili Azure Firewall Manager, nastavení směrování se automaticky naplní do **výchozí směrovací tabulky**. Privátní provoz se vztahuje na virtuální síť a větve a internetový provoz se vztahuje na 0.0.0.0/0.

Připojení VPN, ExpressRoute a User VPN se společně nazývají větve a přiřadí ke stejné (výchozí) směrovací tabulce. Všechna připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek. Chcete-li nakonfigurovat tento scénář, proveďte následující kroky:

1. Vytvořte vlastní směrovací tabulku **RT_VNet**.
1. Vytvořte trasu pro aktivaci typu VNet-to-Internet a VNet-to-větvi: 0.0.0.0/0 s dalším segmentem směrování odkazujícím na Azure Firewall. V části šíření se ujistěte, že je vybraná možnost virtuální sítě, která by zajistila konkrétnější trasy, což umožňuje přímý přenos dat VNet-to-VNet.

   * V části **asociace:** vyberte virtuální sítě, která bude znamenat, že virtuální sítě bude dosahovat cíle v závislosti na trasách této směrovací tabulky.
   * V části **šíření:** vyberte virtuální sítě, která bude znamenat, že virtuální sítě se šíří do této směrovací tabulky. Jinými slovy, další konkrétní trasy se rozšíří do této směrovací tabulky, čímž se zajistí přímý přenos provozu mezi virtuální sítí a virtuální sítí.

1. Přidejte agregovanou statickou trasu pro virtuální sítě do **výchozí směrovací tabulky** , která umožňuje aktivovat tok mezi virtuálními sítěmi prostřednictvím Azure firewall.

   * Nezapomeňte, že větve jsou přidružené a šíří se do výchozí směrovací tabulky.
   * Větve se nešíří do RT_VNet směrovací tabulky. Tím se zajistí tok přenosů mezi virtuálními sítěmi prostřednictvím Azure Firewall.

Výsledkem bude, že se změní konfigurace směrování, jak je znázorněno na **obrázku 1**.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Obrázek 1":::

> [!NOTE]
> Virtuální rozbočovače WAN a připojené virtuální sítě by měly být ve stejné oblasti Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
* Další informace o tom, jak nakonfigurovat směrování virtuálního rozbočovače, najdete v tématu [Jak konfigurovat směrování virtuálního rozbočovače](how-to-virtual-hub-routing.md).
