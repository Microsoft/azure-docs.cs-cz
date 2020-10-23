---
title: 'Scénář: Any-to-Any'
titleSuffix: Azure Virtual WAN
description: Scénáře pro směrování any-to-Any
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 09dddad24794491b53a11f7b0e4347f43f11598b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440480"
---
# <a name="scenario-any-to-any"></a>Scénář: Any-to-Any

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V jakémkoli scénáři je možné, že každý paprsek má přístup k jinému paprsku. Pokud existuje víc rozbočovačů, je ve výchozím nastavení Standard Virtual WAN povolené směrování centra (označované také jako u služby Inter-hub). Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Návrh

Aby bylo možné zjistit, kolik směrovacích tabulek bude potřeba ve scénáři virtuální sítě WAN, můžete vytvořit matici připojení, kde každá buňka představuje, jestli zdroj (řádek) může komunikovat s cílovým sloupcem (sloupec).

| Z |   Záměr |  *Virtuální sítě* | *Větve* |
| -------------- | -------- | ---------- | ---|
| Virtuální sítě     | &#8594;| Direct | Direct |
| Větve   | &#8594;| Direct  | Direct |

Každá z buněk v předchozí tabulce popisuje, jestli připojení k virtuální síti WAN (strana "od" na straně toku, záhlaví řádků) komunikuje s předponou cíle (stranu "do" toku, záhlaví sloupce kurzívou). V tomto scénáři nejsou k dispozici žádné brány firewall ani síťová virtuální zařízení, takže komunikace toků přímo přes virtuální síť WAN (tedy slovo "Direct" v tabulce).

Vzhledem k tomu, že všechna připojení z virtuální sítě i větví (VPN, ExpressRoute a User VPN) mají stejné požadavky na připojení, vyžaduje se jedna směrovací tabulka. V důsledku toho budou všechna připojení přidružená a šířena ke stejné směrovací tabulce, výchozí směrovací tabulce:

* Virtuální sítě:
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **výchozí**
* Zřizování
  * Přidružená tabulka směrování: **výchozí**
  * Rozšiřování do směrovacích tabulek: **výchozí**

Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Na **obrázku 1**se může vzájemně navázat všechny virtuální sítě a větve (VPN, EXPRESSROUTE, P2S). Ve virtuálním rozbočovači fungují připojení následujícím způsobem:

* Připojení VPN připojuje síť VPN k bráně VPN.
* Připojení k virtuální síti připojuje virtuální síť k virtuálnímu rozbočovači. Směrovač virtuálního rozbočovače poskytuje funkci přenosu mezi virtuální sítě.
* Připojení ExpressRoute připojuje okruh ExpressRoute k bráně ExpressRoute.

Tato připojení (ve výchozím nastavení při vytváření) jsou přidružená k výchozí směrovací tabulce, pokud nenastavíte konfiguraci směrování pro připojení buď na **žádná**, nebo na vlastní směrovací tabulku. Tato připojení také šíří trasy ve výchozím nastavení do výchozí směrovací tabulky. To je to, co umožňuje libovolně libovolnému scénáři, kdy se může každý paprskový (virtuální síť, VPN, ER, P2S) vzájemně navázat.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Obrázek 1":::

## <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Tento scénář je ve výchozím nastavení povolený pro standard Virtual WAN. Pokud je nastavení pro větev mezi větvemi v konfiguraci sítě WAN zakázané, zakážete připojení mezi větvemi paprsků. VPN/ExpressRoute/Uživatelská síť VPN se považují za větvení ve virtuální síti WAN.

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
