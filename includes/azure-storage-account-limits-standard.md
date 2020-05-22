---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/20/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 72afa8ebea4cb9bf07cb3ce56583cbdf1849be9e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778286"
---
V následující tabulce jsou popsány výchozí limity pro účty úložiště Azure pro obecné účely V1, v2, BLOB Storage a Block. Limit *ingress* příchozího přenosu dat odkazuje na všechna data, která se odesílají do účtu úložiště. Limit *odchozího* přenosu dat odkazuje na všechna data přijatá z účtu úložiště.

| Prostředek | Omezení |
| --- | --- |
| Počet účtů úložiště podle jednotlivých oblastí a předplatných, včetně účtů úložiště Standard a Premium.| 250 |
| Maximální kapacita účtu úložiště | 5 PiB <sup>1</sup>|
| Maximální počet kontejnerů objektů blob, objektů blob, sdílených složek, tabulek, front, entit nebo zpráv na účet úložiště | Bez omezení |
| Maximální počet požadavků<sup>1</sup> na účet úložiště | počet požadavků 20 000 za sekundu |
| Maximální příchozí<sup>1</sup> na účet úložiště (oblasti USA, Evropa) | 25 GB/s |
| Maximální příchozí<sup>1</sup> na účet úložiště (jiné oblasti než USA a Evropa) | 5 GB/s, pokud je povolená možnost RA-GRS/GRS, 10 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální výstup pro účty pro obecné účely v2 a BLOB Storage (všechny oblasti) | 50 GB/s |
| Maximální výstup pro účty úložiště V1 pro obecné účely (oblasti USA) | 20 GB/s, pokud je povolená možnost RA-GRS/GRS, 30 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální výstup pro účty úložiště pro obecné účely V1 (oblasti mimo USA) | 10 GB/s, pokud je povolená možnost RA-GRS/GRS, 15 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální počet pravidel virtuální sítě na účet úložiště | 200 |
| Maximální počet pravidel IP adres na účet úložiště | 200 |

<sup>1</sup> Azure Storage standardní účty podporují vyšší limity kapacity a vyšší limity pro příchozí zpracování žádostí. Pokud chcete požádat o zvýšení limitů účtu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Pokud váš účet úložiště má povolený přístup pro čtení s geograficky redundantním úložištěm (RA-GRS) nebo geograficky redundantním úložištěm (RA-GZRS), pak jsou cíle výstupu sekundárního umístění stejné jako v primárním umístění. Mezi možnosti [replikace Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) patří:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na službě Azure Blob Storage.

> [!NOTE]
> Microsoft doporučuje, abyste pro většinu scénářů používali účet úložiště pro obecné účely v2. Účet Azure Blob Storage pro obecné účely v1 nebo Azure můžete snadno upgradovat na účet pro obecné účely v2 bez výpadků a bez nutnosti kopírovat data. Další informace najdete v tématu [upgrade na účet úložiště pro obecné účely v2](../articles/storage/common/storage-account-upgrade.md).

Pokud požadavky vaší aplikace překračují cíle škálovatelnosti jednoho účtu úložiště, můžete vytvořit aplikaci pro používání více účtů úložiště. Datové objekty pak můžete rozdělit do oddílů v rámci těchto účtů úložiště. Informace o cenách na svazcích najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).

Všechny účty úložiště běží v ploché síťové topologii bez ohledu na to, kdy byly vytvořeny. Další informace o architektuře Azure Storage ploché sítě a o škálovatelnosti najdete v článku [Microsoft Azure Storage: vysoce dostupná služba cloudového úložiště s silnou konzistencí](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
