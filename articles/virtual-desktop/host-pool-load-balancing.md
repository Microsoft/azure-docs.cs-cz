---
title: Virtuální Desktop Preview Windows hostitele fondu metodách Vyrovnávání zatížení – Azure
description: Hostitel fondu metodách Vyrovnávání zatížení pro prostředí Windows virtuální plochy, ve verzi Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 8b18224339654c067d8ab9b543fa49a9c7d55ddd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870519"
---
# <a name="host-pool-load-balancing-methods"></a>Metody vyrovnávání zatížení fondu hostitelů

Virtuální Desktop Preview Windows podporuje dvě metody vyrovnávání zatížení. Každá metoda určuje hostitele relace, který bude hostitelem relace uživatele při připojení k prostředku v rámci fondu hostitele.

Následující metody vyrovnávání zatížení jsou k dispozici v virtuální plochy Windows:

- Šířka první zátěže umožňuje rovnoměrně distribuovat napříč hostiteli relace ve fondu hostitele uživatelských relací.
- Hloubka první zátěže umožňuje saturate hostitele relace s uživatelských relací v rámci fondu hostitele. Po první relaci dosáhne prahové hodnoty omezení relace, přesměruje nástroje pro vyrovnávání zatížení nových uživatelských připojení na další hostitele relace ve fondu hostitele, dokud nedosáhne její omezení a tak dále.

Každý fond hostitele lze konfigurovat pouze jeden typ Vyrovnávání zatížení pro ně specifická. Ale i Vyrovnávání zatížení metody sdílenou složku fond hostování následujících chování bez ohledu na to, které jsou v:

- Pokud uživatel už má relaci ve fondu hostitele a se znovu připojuje k této relaci, nástroje pro vyrovnávání zatížení se úspěšně přesměruje je to hostitele relace s jejich stávajících relací. Toto chování platí i v případě, že tohoto hostitele relace AllowNewConnections je nastavena na hodnotu False.
- Pokud uživatel ještě nemá relaci ve fondu hostitele, nástroj pro vyrovnávání zatížení se nezabývá relace hostitele, jehož vlastnost AllowNewConnections je nastavena na hodnotu False během vyrovnávání zatížení.

## <a name="breadth-first-load-balancing-method"></a>Šířka první metodu Vyrovnávání zatížení

Metoda Vyrovnávání zatížení šířka první umožňuje distribuci uživatelských připojení k optimalizaci pro tento scénář. Tato metoda je ideální pro organizace, které chtějí poskytnout nejlepší prostředí pro uživatele, připojení k jejich ve fondu prostředí virtuálního klienta.

Tato metoda šířka první nejprve dotazuje hostitele relace, které umožňují nové připojení. Metoda pak vybere hostitele relace s nejmenším počtem relací. Pokud existuje rovnosti, metoda vybere první hostitele relace v dotazu.

## <a name="depth-first-load-balancing-method"></a>Hloubka první metodu Vyrovnávání zatížení

Metoda Vyrovnávání zatížení hloubky první umožňuje saturate jednoho hostitele relace současně optimalizovat pro tento scénář. Tato metoda je ideální pro organizace hledí na cenu, které chcete podrobnější řízení počtu virtuálních počítačů, které jste přiřadili fond hostitele.

Tato metoda hloubky první nejprve dotazuje hostitele relace, povolit nová připojení, jež nebyly pryč nedosáhli příslušného limitu maximální časový limit relace. Metoda pak vybere hostitele relace s nejvyšším počtem relací. Pokud existuje rovnosti, metoda vybere první hostitele relace v dotazu.