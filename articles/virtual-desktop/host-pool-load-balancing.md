---
title: Vyrovnávání zatížení fondu hostitelů virtuálních počítačů s Windows – Azure
description: Přečtěte si informace o metodách vyrovnávání zatížení fondu hostitelů pro prostředí virtuálních počítačů s Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 00870de007d590de33881809c57107997b531d8c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285131"
---
# <a name="host-pool-load-balancing-methods"></a>Metody vyrovnávání zatížení fondu hostitelů

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Virtuální plocha Windows podporuje dvě metody vyrovnávání zatížení. Každá metoda určuje, který hostitel relace bude hostovat relaci uživatele, když se připojí k prostředku ve fondu hostitelů.

Na virtuálním počítači s Windows jsou k dispozici následující metody vyrovnávání zatížení:

- Šířka – první vyrovnávání zatížení umožňuje rovnoměrně rozdělit uživatelské relace mezi hostitele relací v hostitelském fondu.
- Špičkové vyrovnávání zatížení umožňuje naplnění hostitele relace pomocí uživatelských relací ve fondu hostitelů. Jakmile první relace dosáhne prahové hodnoty limitu relace, nástroj pro vyrovnávání zatížení směruje všechna nová připojení uživatelů k dalšímu hostiteli relace ve fondu hostitelů, dokud nedosáhne svého limitu, a tak dále.

Každý fond hostitelů může nakonfigurovat pouze jeden typ vyrovnávání zatížení, který je pro něj specifický. Obě metody vyrovnávání zatížení ale sdílejí následující chování bez ohledu na to, ve kterém fondu hostitelů jsou:

- Pokud uživatel už má relaci ve fondu hostitelů a znovu se k této relaci připojuje, nástroj pro vyrovnávání zatížení je úspěšně přesměruje na hostitele relace s existující relací. Toto chování platí i v případě, že je vlastnost AllowNewConnections hostitele relace nastavena na hodnotu false.
- Pokud uživatel ještě nemá relaci ve fondu hostitelů, pak Nástroj pro vyrovnávání zatížení nebere v úvahu, že při vyrovnávání zatížení je vlastnost AllowNewConnections nastavena na hodnotu false.

## <a name="breadth-first-load-balancing-method"></a>Šířka – první metoda vyrovnávání zatížení

Metoda škálování na první vyrovnávání zatížení umožňuje distribuovat uživatelská připojení k optimalizaci pro tento scénář. Tato metoda je ideální pro organizace, které chtějí poskytovat nejlepší možnosti pro uživatele, kteří se připojují k prostředí virtuálních klientů ve fondu.

Metoda geografického prvního dotazu nejprve dotazuje hostitele relací, kteří umožňují nová připojení. Metoda pak vybere hostitele relace s nejmenším počtem relací. Pokud je k dispozici, metoda vybere první hostitele relace v dotazu.

## <a name="depth-first-load-balancing-method"></a>Metoda vyrovnávání zatížení první hloubky

Metoda hloubkového vyrovnávání zatížení umožňuje naplnění jednoho hostitele relace v čase k optimalizaci pro tento scénář. Tato metoda je ideální pro organizace s důrazem na náklady, které mají podrobnější kontrolu nad počtem virtuálních počítačů, které jsou přiděleny pro fond hostitelů.

První metoda se první dotazuje na hostitele relací, které povolují nová připojení a neprošly maximálním limitem relace. Metoda pak vybere hostitele relace s největším počtem relací. Pokud existuje, metoda vybere první hostitele relace v dotazu.