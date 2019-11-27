---
title: Omezení služby Azure blockchain
description: Přehled služby a funkční omezení ve službě Azure blockchain
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455657"
---
# <a name="limits-in-azure-blockchain-service"></a>Omezení ve službě Azure blockchain

Služba Azure blockchain má omezení služby a funkčnosti, jako je počet uzlů, které může mít člen, omezení konsorcia a množství úložiště.

## <a name="pricing-tier"></a>Cenová úroveň

Maximální limity pro transakce a uzly validátoru závisí na tom, jestli zřizujete službu Azure blockchain na úrovních Basic nebo Standard.

| Cenová úroveň | Maximální počet uzlů transakcí | Maximální počet uzlů validátoru |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.

## <a name="storage-capacity"></a>Kapacita úložiště

Maximální velikost úložiště, které je možné použít pro jednotlivé uzly pro data a protokoly hlavní knihy, je 1,8 terabajtů.

Zmenšení hlavní knihy a velikosti úložiště protokolů se nepodporuje.

## <a name="consortium-limits"></a>Omezení konsorcia

* **Názvy konsorcia a členů musí být** v rámci služby Azure blockchain jedinečné od ostatních názvů Consortium a member.

* **Názvy členů a konsorcia se nedají změnit.**

* **Všichni členové konsorcia musí být ve stejné cenové úrovni.**

* **Všichni členové, kteří se účastní konsorcium, se musí nacházet ve stejné oblasti.**

    První člen vytvořený v konsorciu určuje oblast. Pozvaní členové do konsorcia musí být ve stejné oblasti jako první člen. Omezení všech členů na stejnou oblast vám pomůže zajistit, aby se v síti nepříznivě ovlivnila shoda.

* **Konsorcium musí mít aspoň jednoho správce.**

    Pokud má konsorcium pouze jednoho správce, nemůže z konsorcia odstranit ani odstranit jeho člena, dokud není do konsorcia přidán nebo povýšen jiný správce.

* **Členy odebrané z konsorcia nelze znovu přidat.**

    Místo toho je nutné je znovu pozvat, aby se připojili k konsorciu a vytvořil nový člen. Jejich stávající členské prostředky nejsou odstraněny pro zachování historických transakcí.

* **Všichni členové konsorcia musí používat stejnou verzi hlavní knihy.**

    Další informace o opravách, aktualizacích a verzích hlavní knihy dostupných ve službě Azure blockchain najdete v článku o [opravách, aktualizacích a verzích](ledger-versions.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o zásadách pro opravy a upgrady systémů – [opravy, aktualizace a verze](ledger-versions.md).
