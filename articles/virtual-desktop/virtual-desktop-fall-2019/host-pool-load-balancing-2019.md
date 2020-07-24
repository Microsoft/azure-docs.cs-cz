---
title: Vyrovnávání zatížení fondu hostitelů virtuálních počítačů s Windows (2019) – Azure
description: Přečtěte si informace o metodách vyrovnávání zatížení fondu hostitelů pro prostředí pro virtuální plochu s Windows (verze 2019).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 50819fc6b1e13aeb1c0eddf306ecdfbe8147a44f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046272"
---
# <a name="host-pool-load-balancing-methods-fall-2019-release"></a>Metody vyrovnávání zatížení fondu hostitelů (vydání 2019 verze)

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../host-pool-load-balancing.md).

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