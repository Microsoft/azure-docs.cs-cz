---
title: Obnovení databáze Oracle v infrastruktuře Azure BareMetal
description: Přečtěte si, jak můžete obnovit databázi Oracle v infrastruktuře Azure BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590227"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Obnovení databáze Oracle v infrastruktuře Azure BareMetal

I když žádná jediná technologie není chráněná ze všech scénářů selhání, funkce kombinování nabízí správcům databáze možnost obnovit svou databázi prakticky v jakékoli situaci.

## <a name="causes-of-database-failure"></a>Příčiny selhání databáze

K selháním databáze může dojít z mnoha důvodů, ale typicky spadají do několika kategorií:

- Chyby manipulace s daty
- Ztráta online protokolů opětovného provedení.
- Ztráta databázových řídicích souborů.
- Ztrátou databázových souborů databáze.
- Fyzické poškození dat.

## <a name="choose-your-method-of-recovery"></a>Zvolit metodu obnovení

Typ obnovení závisí na typu selhání. Řekněme, že objekt je vyřazený nebo data jsou nesprávně upravená. Nejrychlejší řešení se pak obvykle provede operací databáze Flashback. V jiných případech může obnovení pomocí Azure NetApp Files snímku poskytnout požadované obnovení. Následující obrázek znázorňuje běžné scénáře selhání a obnovení, pokud jsou implementované všechny možnosti ochrany dat popsané výše.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Diagram rozhodovacího stromu obnovení databáze" lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Mějte na paměti, že tento ukázkový rozhodovací strom je zobrazen pouze z objektivního čočky správce databáze. Každé nasazení může mít různé požadavky, které mohou změnit pořadí voleb. Například provedení role databáze přepnutím do jiné oblasti prostřednictvím ochrany dat může mít nepříznivý vliv na výkon aplikace. Metoda obnovení snímku by mohla získat nižší RTO. Aby se zajistilo splnění požadavků na RTO/RPO, doporučujeme tyto operace provést a vytvořit dokumentované postupy, které se v případě potřeby spustí.

## <a name="next-steps"></a>Další kroky

Další informace o infrastruktuře BareMetal:

- [Co je BareMetal infrastruktura v Azure?](../../concepts-baremetal-infrastructure-overview.md)
- [Propojení instancí infrastruktury BareMetal v Azure](../../connect-baremetal-infrastructure.md)
