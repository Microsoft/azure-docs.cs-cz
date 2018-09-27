---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396119"
---
Následující tabulka popisuje výchozí omezení pro službu Azure Storage. *Příchozího přenosu dat* limit odkazuje na všechna data (požadavky) odesílané do účtu úložiště. *Odchozího přenosu dat* limit odkazuje na všechna data (požadavky) přijímané z účtu úložiště.

| Prostředek | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště na oblast a předplatné, včetně účtů standard a premium | 200 |
| Maximální kapacity účtu úložiště<sup>1</sup> | 500 TiB |
| Maximální počet kontejnerů objektů blob, objekty BLOB, sdílené složky, tabulky, fronty, entity nebo zpráv za účet úložiště | Bez omezení |
| Maximální frekvence požadavků<sup>1</sup> jeden účet úložiště | 20 000 požadavků za sekundu |
| Maximální příchozí<sup>1</sup> jeden účet úložiště (nám oblasti) | 10 GB/s je-li povolena RA-GRS/GRS, 20 GB/s pro LRS nebo ZRS<sup>2</sup> |
| Maximální počet odchozího přenosu dat<sup>1</sup> jeden účet úložiště (nám oblasti) | 20 GB/s je-li povolena RA-GRS/GRS, 30 GB/s pro LRS nebo ZRS |
| Maximální příchozí<sup>1</sup> jeden účet úložiště (oblasti mimo USA) | 5 GB/s je-li povolena RA-GRS/GRS, 10 GB/s pro LRS nebo ZRS<sup>2</sup> |
| Maximální počet odchozího přenosu dat<sup>1</sup> jeden účet úložiště (oblasti mimo USA) | 10 GB/s je-li povolena RA-GRS/GRS, 15 GB/s pro LRS nebo ZRS |

<sup>1</sup> účty úložiště azure podporují vyšší limity pro kapacitu, frekvence požadavků, příchozí i odchozí požadavkem. Další informace o zvýšení limitů najdete v tématu [oznamujeme účty úložiště a větší měřítko](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). Chcete-li požádat o navýšení limitů účtu, obraťte se na [podpory Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>[replikace azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) mezi možnosti patří:
* **RA-GRS**: geograficky redundantní úložiště jen pro čtení. Pokud je povolené RA-GRS, jsou stejné jako pro primární umístění cíle odchozího přenosu dat pro sekundární lokality.
* **GRS**: geograficky redundantní úložiště. 
* **ZRS**: zónově redundantní úložiště.
* **LRS**: místně redundantní úložiště. 

