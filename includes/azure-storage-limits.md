---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/23/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84333b26ac70db4b400f7236d4255f4b57a6ca7d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571982"
---
Následující tabulka popisuje výchozí omezení pro službu Azure Storage. *Příchozího přenosu dat* limit odkazuje na všechna data (požadavky) odesílané do účtu úložiště. *Odchozího přenosu dat* limit odkazuje na všechna data (požadavky) přijímané z účtu úložiště.

| Prostředek | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště na oblast a předplatné, včetně účtů standard a premium | 250 |
| Maximální kapacity účtu úložiště | 2 PB pro USA a Evropě, 500 TB pro všechny ostatní oblasti včetně Velká Británie |
| Maximální počet kontejnerů objektů blob, objekty BLOB, sdílené složky, tabulky, fronty, entity nebo zpráv za účet úložiště | Bez omezení |
| Maximální frekvence požadavků<sup>1</sup> jeden účet úložiště | 20 000 požadavků za sekundu |
| Maximální příchozí<sup>1</sup> jeden účet úložiště (nám oblasti) | 10 GB/s je-li povolena RA-GRS/GRS, 20 GB/s pro LRS nebo ZRS<sup>2</sup> |
| Maximální příchozí<sup>1</sup> jeden účet úložiště (oblasti mimo USA) | 5 GB/s je-li povolena RA-GRS/GRS, 10 GB/s pro LRS nebo ZRS<sup>2</sup> |
| Maximální počet odchozího přenosu dat pro obecné účely v2 a účty Blob storage (všechny oblasti) | 50 GB/s |
| Maximální počet odchozího přenosu dat pro účty služby storage pro obecné účely v1 (oblasti v USA) | 20 GB/s je-li povolena RA-GRS/GRS, 30 GB/s pro LRS nebo ZRS <sup>2</sup> |
| Maximální počet odchozího přenosu dat pro účty úložiště pro obecné účely v1 (oblasti mimo USA) | 10 GB/s je-li povolena RA-GRS/GRS, 15 GB/s pro LRS nebo ZRS <sup>2</sup> |

<sup>1</sup> účty úložiště azure podporují vyšší limity pro příchozí přenos dat podle požadavku. Chcete-li požádat o zvýšení v omezení účtu pro příchozí přenos dat, obraťte se na [podpory Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>[replikace azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) mezi možnosti patří:
* **RA-GRS**: geograficky redundantní úložiště jen pro čtení. Pokud je povolené RA-GRS, jsou stejné jako pro primární umístění cíle odchozího přenosu dat pro sekundární lokality.
* **GRS**: geograficky redundantní úložiště. 
* **ZRS**: zónově redundantní úložiště.
* **LRS**: místně redundantní úložiště. 

Potřeb svojí aplikace překročení cíle škálovatelnosti z jednoho účtu úložiště, můžete vytvářet aplikace pomocí více účtů úložiště. Pak můžete dělit datové objekty mezi těmito účty úložiště. Zobrazit [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) informace o cenách.

Všechny účty úložiště spustit na ploché síťové topologie a podporu škálovatelnost a výkonnostní cíle, které jsou popsané v tomto článku, bez ohledu na to, když byly vytvořeny. Další informace o architektuře ploché sítě služby Azure Storage a na škálovatelnost, naleznete v tématu [Microsoft Azure Storage: A vysoce dostupné služby cloudového úložiště se silnou konzistencí](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

