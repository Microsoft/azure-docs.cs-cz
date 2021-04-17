---
title: Vysoká dostupnost a zotavení po havárii pro Oracle v BareMetal
description: Přečtěte si o vysoké dostupnosti a zotavení po havárii pro Oracle v infrastruktuře Azure BareMetal.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590235"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Vysoká dostupnost a zotavení po havárii pro Oracle v BareMetal

V tomto článku se podíváme na základy vysoké dostupnosti a zotavení po havárii. Pak budeme zavádět, jak můžete dosáhnout vysoké dostupnosti a zotavení po havárii v prostředí Oracle v infrastruktuře BareMetal.

## <a name="high-availability-vs-disaster-recovery"></a>Vysoká dostupnost vs. zotavení po havárii

Vysoká dostupnost a zotavení po havárii poskytují pokrytí, ale z různých typů selhání. Používají různé funkce a možnosti Oracle Database.

Vysoká dostupnost umožňuje systému překonat více selhání, aniž by to ovlivnilo uživatelské prostředí aplikace. Mezi běžné charakteristiky vysoce dostupného systému patří:

- Redundantní hardware, který nemá žádný jediný bod selhání.
- Automatické obnovení z nekritického selhání, například neúspěšné diskové jednotky nebo vadné síťové kabely.
- Možnost navrátit změny hardwaru a softwaru bez jakýchkoli znatelného účinku na zpracování.
- Splňuje nebo překračuje cíle pro cíle pro čas obnovení (RTO) a cíle bodu obnovení (RPO).

Nejběžnější funkcí Oracle používaných pro vysokou dostupnost jsou [clustery s aplikacemi Oracle reálné aplikace (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92).

Zotavení po havárii chrání před neobnovitelnými lokalizovanými chybami, které by snížit vaši primární strategii vysoké dostupnosti. V ekosystému Oracle je k dispozici prostřednictvím replikace databáze, označovanou také jako [Ochrana dat Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590).

## <a name="next-steps"></a>Další kroky

Další informace o funkcích vysoké dostupnosti pro Oracle:

> [!div class="nextstepaction"]
> [Funkce vysoké dostupnosti pro infrastrukturu Oracle v BareMetal](high-availability-features.md)
