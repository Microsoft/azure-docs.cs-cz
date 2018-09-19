---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/13/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fb349c9ea2178d84e367e763797d5d93d3ae4c53
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46299104"
---
Následující tabulka popisuje výchozí omezení pro službu Azure Storage. *Příchozího přenosu dat* limit odkazuje na všechna data (požadavky) odesílané do účtu úložiště. *Odchozího přenosu dat* limit odkazuje na všechna data (požadavky) přijímané z účtu úložiště.

| Prostředek | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště na oblast a předplatné, včetně účtů standard a premium | 200 |
| Maximální kapacity účtu úložiště | 500 TiB |
| Maximální počet kontejnerů objektů blob, objekty BLOB, sdílené složky, tabulky, fronty, entity nebo zpráv za účet úložiště | Bez omezení |
| Maximální frekvence požadavků na účet úložiště | 20 000 požadavků za sekundu |
| Maximální příchozí přenos dat na účet úložiště (nám oblasti) | 10 GB/s je-li povolena RA-GRS/GRS, 20 GB/s pro LRS nebo ZRS<sup>1</sup> |
| Maximální počet odchozího přenosu dat za účet úložiště (nám oblasti) | 50 GB/s |
| Maximální příchozí přenos dat na účet úložiště (oblasti mimo USA) | 5 GB/s je-li povolena RA-GRS/GRS, 10 GB/s pro LRS nebo ZRS<sup>1</sup> |
| Maximální počet odchozího přenosu dat za účet úložiště (oblasti mimo USA) | 50 GB/s |

<sup>1</sup>[replikace azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) mezi možnosti patří:
* **RA-GRS**: geograficky redundantní úložiště jen pro čtení. Pokud je povolené RA-GRS, jsou stejné jako pro primární umístění cíle odchozího přenosu dat pro sekundární lokality.
* **GRS**: geograficky redundantní úložiště. 
* **ZRS**: zónově redundantní úložiště.
* **LRS**: místně redundantní úložiště. 

