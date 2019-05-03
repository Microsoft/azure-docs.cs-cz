---
title: Azure Blockchain Limits
description: Přehled služby Azure a funkční omezení ve službě Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028167"
---
# <a name="limits-in-azure-blockchain-service"></a>Omezení ve službě Azure Blockchain

Služba Azure Blockchain má služby a funkční omezení, jako je počet uzlů, které může mít člena, consortium omezení a úložiště částky.

## <a name="pricing-tier"></a>Cenová úroveň

Maximální počtu transakcí a uzly program pro ověření závisí na tom, jestli si zřídíte službu Azure Blockchain na Basic nebo Standard cenové úrovně.

| Cenová úroveň | Maximální počet transakcí uzly | Maximální počet uzlů program pro ověření |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Po vytvoření členu není podporováno při změně cenové úrovně mezi Basic a Standard.

## <a name="storage-capacity"></a>Kapacita úložiště

Maximální velikost úložiště, které je možné použít pro uzel knihy dat a protokolů je 1 terabajt.

Snížení velikosti úložiště knihy a protokolu se nepodporuje.

## <a name="consortium-limits"></a>Omezení W3C

* **Názvy Consortium a člena musí být jedinečné** od jiných názvů consortium a členů ve službě Azure Blockchain.

* **Názvy členů a consortium nelze změnit.**

* **Všechny členy konsorcium musí být v rámci stejné cenové úrovně**

* **Všechny členy, které jsou součástí konsorcium se musí nacházet ve stejné oblasti**

    První člen vytvořené v konsorcium určuje oblast. Pozvaní členové pro konsorcia se musí nacházet ve stejné oblasti jako první člen. Omezení členů do stejné oblasti ujistil, že caiq sítě není mít negativní dopad.

* **Konsorcium musí mít aspoň jednoho správce**

    Pokud je v konsorcium jen jednoho správce, nemohou sami odebrat z konsorcia nebo odstranit jejich členů, dokud se přidá nebo propagovány konsorcia jiným správcem.

* **Odebrat z konsorcia členy nejde znovu přidat.**

    Místo toho se musí se znovu pozvat spojit konsorcia a vytvoření nového člena. Jejich existující prostředek člen nejsou odstranit pro zachování historie transakcí.

* **Všechny členy konsorcium musí používat stejnou verzi účetní kniha**

    Další informace o použití dílčích oprav, aktualizace a verze knihy ve službě Azure Blockchain k dispozici, najdete v části [opravy, aktualizace a verze](ledger-versions.md).

## <a name="next-steps"></a>Další postup

* [Použití dílčích oprav, aktualizace a verze](ledger-versions.md)
