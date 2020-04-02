---
title: Limity služby Azure Blockchain
description: Přehled limitů služeb a funkcí ve službě Azure Blockchain Service
ms.date: 03/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c728e617ac37795988cd596c7cb0c5025aac4ccf
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529578"
---
# <a name="limits-in-azure-blockchain-service"></a>Limity ve službě Azure Blockchain

Služba Azure Blockchain Service má omezení služeb a funkcí, jako je počet uzlů, které může mít člen, omezení konsorcia a částky úložiště.

## <a name="pricing-tier"></a>Cenová úroveň

Maximální limity pro transakce a uzly validátoru závisí na tom, jestli zřazujete službu Azure Blockchain na základních nebo standardních cenových úrovních.

| Cenová úroveň | Maximální počet uzly transakcí | Maximální validátoru uzly |
|:---|:---:|:---:|
| Základní | 10 | 1 |
| Standard | 10 | 2 |

Vaše síť konsorcia by měla mít alespoň dva uzly standardní úrovně služby Azure Blockchain Service. Standardní uzly úrovně zahrnují dva validátoru uzly. Čtyři validátoru uzly jsou povinny splnit [Istanbul byzantské odolnosti proti chybám konsensu](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Základní úroveň je určenpro vývoj, testování a testování konceptů. Použijte standardní úroveň pro nasazení produkční třídy. Úroveň *Standard* byste měli použít také v případě, že používáte Správce dat Blockchain nebo odesíláte velký objem soukromých transakcí.

Změna cenové úrovně mezi základní a standardní po vytvoření člena není podporována.

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

    Spíše musí být znovu vyzváni, aby se připojili ke konsorciu a vytvořili nového člena. Jejich existující členské prostředky nejsou odstraněny zachovat historické transakce.

* **Všichni členové v konsorciu musí používat stejnou verzi hlavní knihy.**

    Další informace o opravách, aktualizacích a verzích hlavní knihy dostupných ve službě Azure Blockchain service najdete v [tématu Opravy, aktualizace a verze](ledger-versions.md).

## <a name="next-steps"></a>Další kroky

Další informace o zásadách týkajících se oprav a upgradů systémů – [Opravy, aktualizace a verze](ledger-versions.md).
