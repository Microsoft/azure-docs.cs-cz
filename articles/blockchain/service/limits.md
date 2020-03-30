---
title: Limity služby Azure Blockchain
description: Přehled limitů služeb a funkcí ve službě Azure Blockchain Service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455657"
---
# <a name="limits-in-azure-blockchain-service"></a>Limity ve službě Azure Blockchain

Služba Azure Blockchain Service má omezení služeb a funkcí, jako je počet uzlů, které může mít člen, omezení konsorcia a částky úložiště.

## <a name="pricing-tier"></a>Cenová úroveň

Maximální limity pro transakce a uzly validátoru závisí na tom, jestli zřídíte službu Azure Blockchain na základních nebo standardních cenových úrovních.

| Cenová úroveň | Maximální počet uzly transakcí | Maximální validátoru uzly |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Změna cenové úrovně mezi Basic a Standard po vytvoření člena není podporována.

## <a name="storage-capacity"></a>Kapacita úložiště

Maximální velikost úložiště, které lze použít na uzel pro data hlavní knihy a protokoly je 1,8 terabajtů.

Snížení velikosti úložiště hlavní knihy a protokolu není podporováno.

## <a name="consortium-limits"></a>Limity konsorcia

* **Názvy konsorcií a členů musí být jedinečné** z jiných názvů konsorcií a členů ve službě Azure Blockchain.

* **Názvy členů a konsorcií nelze změnit.**

* **Všichni členové v konsorciu musí být ve stejné cenové úrovni.**

* **Všichni členové, kteří se účastní konsorcia, musí pobývat ve stejném regionu.**

    První člen vytvořený v konsorciu diktuje oblast. Pozvaní členové konsorcia musí mít bydliště ve stejné oblasti jako první člen. Omezení všech členů na stejnou oblast pomáhá zajistit, že konsensus sítě nebude negativně ovlivněn.

* **Konsorcium musí mít alespoň jednoho správce.**

    Pokud je v konsorciu pouze jeden správce, nemohou se odebrat z konsorcia nebo odstranit svého člena, dokud nebude v konsorciu přidán nebo povýšen jiný správce.

* **Členy odebrané z konsorcia nelze znovu přidat.**

    Spíše musí být znovu vyzváni, aby se připojili ke konsorciu a vytvořili nového člena. Jejich existující členský prostředek nejsou odstraněny zachovat historické transakce.

* **Všichni členové v konsorciu musí používat stejnou verzi hlavní knihy.**

    Další informace o opravách, aktualizacích a verzích hlavní knihy dostupných ve službě Azure Blockchain service najdete v [tématu Opravy, aktualizace a verze](ledger-versions.md).

## <a name="next-steps"></a>Další kroky

Další informace o zásadách týkajících se oprav a upgradů systémů – [Opravy, aktualizace a verze](ledger-versions.md).
