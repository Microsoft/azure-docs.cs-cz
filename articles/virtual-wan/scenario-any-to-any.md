---
title: 'Scénář: Any-to-Any'
titleSuffix: Azure Virtual WAN
description: Scénáře pro směrování any-to-Any
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568402"
---
# <a name="scenario-any-to-any"></a>Scénář: Any-to-Any

Při práci s směrováním virtuálního rozbočovače WAN je k dispozici několik scénářů, které jsou v pořádku. V jakémkoli scénáři je možné, že každý paprsek má přístup k jinému paprsku. Pokud existuje víc rozbočovačů, je ve výchozím nastavení Standard Virtual WAN povolené směrování centra (označované také jako u služby Inter-hub). 

V tomto scénáři jsou připojení VPN, ExpressRoute a User VPN přidružená ke stejné směrovací tabulce. Všechna připojení VPN, ExpressRoute a VPN uživatele šíří trasy do stejné sady směrovacích tabulek. Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architektura scénáře

Na **obrázku 1**se může vzájemně navázat všechny virtuální sítě a větve (VPN, EXPRESSROUTE, P2S). Ve virtuálním rozbočovači fungují připojení následujícím způsobem:

* Připojení VPN připojuje síť VPN k bráně VPN.
* Připojení k virtuální síti připojuje virtuální síť k virtuálnímu rozbočovači. Směrovač virtuálního rozbočovače poskytuje funkci přenosu mezi virtuální sítě.
* Připojení ExpressRoute připojuje okruh ExpressRoute k bráně ExpressRoute.

Tato připojení (ve výchozím nastavení při vytváření) jsou přidružená k výchozí směrovací tabulce, pokud nenastavíte konfiguraci směrování pro připojení buď na **žádná**, nebo na vlastní směrovací tabulku. Tato připojení také šíří trasy ve výchozím nastavení do výchozí směrovací tabulky. To je to, co umožňuje libovolně libovolnému scénáři, kdy se může každý paprskový (virtuální síť, VPN, ER, P2S) vzájemně navázat.

**Obrázek 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Obrázek 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Pracovní postup scénáře

Tento scénář je ve výchozím nastavení povolený pro standard Virtual WAN. Pokud je nastavení pro větev mezi větvemi v konfiguraci sítě WAN zakázané, zakážete připojení mezi větvemi paprsků. VPN/ExpressRoute/Uživatelská síť VPN se považují za větvení ve virtuální síti WAN.

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).
