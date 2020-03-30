---
title: Vyrovnávání zatížení fondu virtuálních desktopů Windows – Azure
description: Metody vyrovnávání zatížení fondu hostitelů pro prostředí Virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127867"
---
# <a name="host-pool-load-balancing-methods"></a>Metody vyrovnávání zatížení fondu hostitelů

Windows Virtual Desktop podporuje dvě metody vyrovnávání zatížení. Každá metoda určuje, který hostitel relace bude hostovat relaci uživatele při připojení k prostředku ve fondu hostitelů.

Ve službě Windows Virtual Desktop jsou k dispozici následující metody vyrovnávání zatížení:

- Vyvažování zatížení na šířku umožňuje rovnoměrně distribuovat uživatelské relace mezi hostiteli relací v hostitelském fondu.
- Hloubka první vyrovnávání zatížení umožňuje nasytit hostitele relace s uživatelskými relacemi v hostitelském fondu. Jakmile první relace dosáhne prahové hodnoty limitu relace, nástroj pro vyrovnávání zatížení přesměruje všechna nová uživatelská připojení na dalšího hostitele relace v hostitelském fondu, dokud nedosáhne svého limitu a tak dále.

Každý fond hostitelů může nakonfigurovat pouze jeden typ vyrovnávání zatížení, který je pro něj specifický. Obě metody vyrovnávání zatížení však sdílejí následující chování bez ohledu na to, ve kterém hostitelském fondu se účastní:

- Pokud uživatel již má relaci ve fondu hostitelů a znovu se k ní připojuje, nástroj pro vyrovnávání zatížení je úspěšně přesměruje na hostitele relace s existující relací. Toto chování platí i v případě, že je vlastnost AllowNewConnections hostitele relace nastavena na hodnotu False.
- Pokud uživatel ještě nemá relaci ve fondu hostitelů, nástroj pro vyrovnávání zatížení nebude považovat hostitele relací, jejichž Vlastnost AllowNewConnections je nastavena na False během vyrovnávání zatížení.

## <a name="breadth-first-load-balancing-method"></a>Metoda vyrovnávání zatížení na šířku

Metoda vyrovnávání zatížení první šířky umožňuje distribuovat uživatelská připojení optimalizovat pro tento scénář. Tato metoda je ideální pro organizace, které chtějí poskytnout nejlepší prostředí pro uživatele, kteří se připojují k jejich sdružené virtuální desktopové prostředí.

Metoda první šířky nejprve dotazuje hostitele relace, které umožňují nová připojení. Metoda pak vybere hostitele relace s nejmenším počtem relací. Pokud je remíza, metoda vybere hostitele první relace v dotazu.

## <a name="depth-first-load-balancing-method"></a>Metoda vyrovnávání zatížení na první hloubku

Metoda vyrovnávání zatížení hloubky první umožňuje nasytit jednoho hostitele relace najednou optimalizovat pro tento scénář. Tato metoda je ideální pro organizace s ohledem na náklady, které chtějí podrobnější kontrolu nad počtem virtuálních počítačů, které přidělili pro fond hostitelů.

Metoda hloubka první dotazy relace hostitelů, které umožňují nová připojení a nepřekročily maximální limit relace. Metoda pak vybere hostitele relace s nejvyšším počtem relací. Pokud je remíza, metoda vybere hostitele první relace v dotazu.