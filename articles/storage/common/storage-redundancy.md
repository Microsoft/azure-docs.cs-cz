---
title: Redundance dat v Azure Storage | Microsoft Docs
description: Data v účtu Microsoft Azure Storage se replikují pro zajištění odolnosti a vysoké dostupnosti. Možnosti redundance zahrnují místně redundantní úložiště (LRS), zónu – redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS), geograficky redundantní úložiště s přístupem pro čtení (RA-GRS), geograficky redundantního úložiště (GZRS) (ve verzi Preview) a geografickou zónu s přístupem pro čtení – redundantní. Storage (RA-GZRS) (verze Preview).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: a4c624cf06a0e56b30b71c80a6b4a5ad48cb31a9
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016393"
---
# <a name="azure-storage-redundancy"></a>Azure Storage redundance

Data na vašem účtu Microsoft Azure Storage se vždy replikují, aby byla zajištěna jejich stálost a vysoká dostupnost. Azure Storage kopíruje vaše data, aby byla chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků výpadků a obrovských přirozených havárií. Můžete se rozhodnout, že budete replikovat data ve stejném datovém centru, napříč datovými centry oblastí v rámci stejné oblasti nebo v geograficky oddělených oblastech.

Replikace zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Storage](https://azure.microsoft.com/support/legal/sla/storage/) i při selhání. Podívejte se do smlouvy SLA na informace o zárukách služby Azure Storage na stálost a dostupnost.

Azure Storage pravidelně ověřuje integritu dat uložených pomocí redundantních kontrol redundance (CRCs). Pokud je zjištěno poškození dat, je opraveno pomocí redundantních dat. Azure Storage taky vypočítává kontrolní součty pro veškerý síťový provoz, aby se zjistilo poškození datových paketů při ukládání nebo načítání dat.

## <a name="choosing-a-redundancy-option"></a>Výběr možnosti redundance

Když vytváříte účet úložiště, můžete vybrat jednu z následujících možností redundance:

* [Místně redundantní úložiště (LRS)](storage-redundancy-lrs.md)
* [Zónově redundantní úložiště (ZRS)](storage-redundancy-zrs.md)
* [Geograficky redundantní úložiště (GRS)](storage-redundancy-grs.md)
* [Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)
* [Geografická zóna – redundantní úložiště (GZRS)](storage-redundancy-gzrs.md)
* [Geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS)](storage-redundancy-gzrs.md)

Následující tabulka poskytuje rychlý přehled rozsahu trvanlivosti a dostupnosti, které vám každá strategie replikace poskytne pro daný typ události (nebo událost podobného dopadu).

| Scénář                                                                                                 | LRS                             | ZRS                              | GRS/RA – GRS                                  | GZRS/RA – GZRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Nedostupnost uzlu v rámci datového centra                                                                 | Ano                             | Ano                              | Ano                                  | Ano                                  |
| Nebudete mít k dispozici celé datové centrum (oblast nebo mimo oblast).                                           | Ne                              | Ano                              | Ano                                  | Ano                                  |
| Výpadek v rámci oblasti                                                                                     | Ne                              | Ne                               | Ano                                  | Ano                                  |
| Přístup pro čtení dat (ve vzdálené geograficky replikované oblasti) v případě nedostupnosti oblasti v rámci regionu | Ne                              | Ne                               | Ano (s RA-GRS)                                   | Ano (s RA-GZRS)                                 |
| Navrženo pro \_ zajištění \_ odolnosti objektů v průběhu daného roku                                          | alespoň 99,999999999% (11 9 's) | minimálně 99,9999999999% (12 9 's) | minimálně 99.99999999999999% (16 9) | minimálně 99.99999999999999% (16 9) |
| Podporované typy účtů úložiště                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| Smlouva SLA o dostupnosti pro žádosti o čtení | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,99% (99,9% pro studenou úroveň přístupu) |
| Smlouva SLA o dostupnosti pro žádosti o zápis | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) |

Všechna data v účtu úložiště se replikují, včetně objektů blob bloku a doplňovacích objektů blob, objektů blob stránky, front, tabulek a souborů. Budou replikovány všechny typy účtů úložiště, i když ZRS vyžaduje účet úložiště pro obecné účely v2.

Informace o cenách pro jednotlivé možnosti redundance najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/). 

Informace o tom, jak Azure Storage garantuje odolnost a dostupnost, najdete v [Azure Storage smlouvě SLA](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Azure Premium Storage podporuje jenom místně redundantní úložiště (LRS).

## <a name="changing-replication-strategy"></a>Změna strategie replikace

Strategii replikace účtu úložiště můžete změnit pomocí [Azure Portal](https://portal.azure.com/), [Azure PowerShellu](storage-powershell-guide-full.md), rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nebo některé z [klientských knihoven Azure Storage](https://docs.microsoft.com/azure/index#pivot=sdkstools). Změna typu replikace účtu úložiště nevede k vyzkoušení času.

   > [!NOTE]
   > V současné době nemůžete použít Azure Portal nebo Azure Storage klientské knihovny k převedení vašeho účtu na ZRS, GZRS nebo RA-GZRS. Pokud chcete migrovat účet do ZRS, přečtěte si téma [redundantní úložiště (ZRS) pro vytváření vysoce dostupných Azure Storage aplikací](storage-redundancy-zrs.md) . Pokud chcete migrovat GZRS nebo RA-GZRS, přečtěte si podrobnosti v [geograficky zóně redundantním úložišti s vysokou dostupností a maximální trvanlivosti (Preview)](storage-redundancy-zrs.md) .
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Existují nějaké náklady na změnu strategie replikace mého účtu?

Záleží na vaší cestě převodu. Seřazení z nejméně na nejdražších, Azure Storagech redundantních nabídek LRS, ZRS, GRS, RA-GRS, GZRS a RA-GZRS. Například *z* LRS na jakýkoli jiný typ replikace budou účtovány další poplatky, protože přecházíte na propracovanější úroveň redundance. Migrace *na* GRS nebo RA-GRS se bude účtovat jako výstupní šířka pásma, protože vaše data (v primární oblasti) se replikují do vzdálené sekundární oblasti. Tento poplatek je jednorázová cena při počátečním nastavení. Po zkopírování dat se neúčtují žádné další poplatky za migraci. Účtuje se vám jenom replikace všech nových nebo aktualizovaných stávajících dat. Podrobnosti o cenách za šířku pásma najdete na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Při migraci účtu úložiště z GRS do LRS se neúčtují žádné další náklady, ale replikovaná data se odstraní ze sekundárního umístění.

Pokud migrujete účet úložiště z RA-GRS na GRS nebo LRS, tento účet se fakturuje jako RA-GRS po dobu dalších 30 dnů od data, kdy se převedlo.

## <a name="see-also"></a>Viz také:

- [Místně redundantní úložiště (LRS): Redundance dat pro Azure Storage s nízkou cenou](storage-redundancy-lrs.md)
- [Redundantní úložiště zóny (ZRS): Vysoce dostupné Azure Storage aplikace](storage-redundancy-zrs.md)
- [Geograficky redundantní úložiště (GRS): Replikace mezi různými oblastmi pro Azure Storage](storage-redundancy-grs.md)
- [Geograficky redundantní úložiště (GZRS) pro vysokou dostupnost a maximální odolnost (Preview)](storage-redundancy-gzrs.md)
- [Azure Storage škálovatelnost a výkonnostní cíle](storage-scalability-targets.md)
- [Návrh vysoce dostupných aplikací s využitím úložiště RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage možností redundance a geograficky redundantního úložiště s přístupem pro čtení](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP papír – Azure Storage: Vysoce dostupná služba cloudového úložiště s silnou konzistencí](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
