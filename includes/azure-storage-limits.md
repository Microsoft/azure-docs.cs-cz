---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6572adb0d8d629910492603a17988b89acce2f17
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755586"
---
| Prostředek | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště na oblast a předplatné | 200<sup>1</sup> |
| Maximální kapacity účtu úložiště | 500 TiB<sup>2</sup> |
| Maximální počet kontejnerů objektů blob, objekty BLOB, sdílené složky, tabulky, fronty, entity nebo zpráv za účet úložiště | Bez omezení |
| Maximální frekvence požadavků na účet úložiště | 20 000 požadavků za sekundu<sup>2</sup> |
| Maximální příchozí<sup>3</sup> jeden účet úložiště (nám oblasti) | 10 GB/s je-li povolena RA-GRS/GRS, 20 GB/s pro LRS nebo ZRS<sup>4</sup> |
| Maximální počet odchozího přenosu dat<sup>3</sup> jeden účet úložiště (nám oblasti) | 20 GB/s je-li povolena RA-GRS/GRS, 30 GB/s pro LRS nebo ZRS<sup>4</sup> |
| Maximální příchozí<sup>3</sup> jeden účet úložiště (oblasti mimo USA) | 5 GB/s je-li povolena RA-GRS/GRS, 10 GB/s pro LRS nebo ZRS<sup>4</sup> |
| Maximální počet odchozího přenosu dat<sup>3</sup> jeden účet úložiště (oblasti mimo USA) | 10 GB/s je-li povolena RA-GRS/GRS, 15 GB/s pro LRS nebo ZRS<sup>4</sup> |

<sup>1</sup>zahrnuje účty úložiště Standard a Premium. Pokud požadujete víc než 200 účtů úložiště v dané oblasti, vytvořte žádost prostřednictvím [podpory Azure](https://azure.microsoft.com/support/faq/). Tým Azure Storage bude váš obchodní případ podívá a může schválit až 250 účtů úložiště pro příslušnou oblast. 

<sup>2</sup> Pokud potřebujete rozšířené limity účtu úložiště, obraťte se prosím [podpory Azure](https://azure.microsoft.com/support/faq/). Tým Azure Storage se žádosti o revizi a může schválit vyšší limity na případ od případu. Obě pro obecné účely a účty úložiště Blob podporují navýšení kapacity, příchozí a odchozí transakce a frekvence požadavků požadavkem. Nové maximální hodnoty pro účty Blob storage, najdete v části [oznamujeme účty úložiště a větší měřítko](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> uzavřeny pouze omezení příchozí a odchozí transakce účtu. *Příchozí přenos dat* odkazuje na všechna data (požadavky) odesílané do účtu úložiště. *Odchozí přenos dat* odkazuje na všechna data (požadavky) přijímané z úložiště účtu.  

<sup>4</sup>možnosti redundance azure Storage patří:
* **RA-GRS**: geograficky redundantní úložiště jen pro čtení. Pokud je povolené RA-GRS, jsou stejné jako pro primární umístění cíle odchozího přenosu dat pro sekundární lokality.
* **GRS**: geograficky redundantní úložiště. 
* **ZRS**: zónově redundantní úložiště.
* **LRS**: místně redundantní úložiště. 
