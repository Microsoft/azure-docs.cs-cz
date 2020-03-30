---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334953"
---
Následující tabulka popisuje výchozí omezení pro účty úložiště azure pro obecné účely v1, v2, úložiště objektů blob, úložiště objektů blob a účty s povoleným úložištěm Data Lake Storage Gen2. Limit *příchozího přenosu dat* odkazuje na všechna data, která jsou odeslána do účtu úložiště. Limit *odchozího přenosu dat* odkazuje na všechna data přijatá z účtu úložiště.

| Prostředek | Omezení |
| --- | --- |
| Počet účtů úložiště na oblast na předplatné, včetně standardních, prémiových a datových lakeových úložišť Gen2 povolených účtů úložiště. <sup>3.</sup> | 250 |
| Maximální kapacita účtu úložiště | 5 PiB <sup>1</sup>|
| Maximální počet kontejnerů objektů BLOB, objektů BLOB, sdílených složek, tabulek, front, entit nebo zpráv na účet úložiště | Bez omezení |
| Maximální míra požadavku<sup>1</sup> na účet úložiště | 20 000 žádostí za sekundu |
| Maximální příchozí přenos dat<sup>1</sup> na účet úložiště (USA, oblasti Evropy) | 25 Gb/s |
| Maximální příchozí přenos dat<sup>1</sup> na účet úložiště (jiné oblasti než USA a Evropa) | 5 Gb/s, pokud je povolenra-GRS/GRS, 10 Gb/s pro LRS/ZRS<sup>2</sup> |
| Maximální odchozí přenos pro účty úložiště pro obecné účely v2 a Blob (všechny oblasti) | 50 Gb/s |
| Maximální odchozí přenos pro účty úložiště pro obecné účely v1 (oblasti USA) | 20 Gb/s, pokud je povolen ra-GRS/GRS, 30 Gb/s pro LRS/ZRS<sup>2</sup> |
| Maximální odchozí přenos pro účty úložiště pro obecné účely v1 (mimo oblasti USA) | 10 Gb/s, pokud je povolenra-GRS/GRS, 15 Gb/s pro LRS/ZRS<sup>2</sup> |
| Maximální počet pravidel virtuální sítě na účet úložiště | 200 |
| Maximální počet pravidel IP adres na účet úložiště | 200 |

<sup>1</sup> Standardní účty Azure Storage podporují vyšší limity kapacity a vyšší limity pro příchozí přenos dat na vyžádání. Chcete-li požádat o zvýšení limitů obchodních účtů, obraťte se na [podporu Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Pokud má váš účet úložiště povolený přístup pro čtení s geograficky redundantním úložištěm (RA-GRS) nebo úložištěm redundantním zónou (RA-GZRS), pak jsou cíle odchozího přenosu pro sekundární umístění shodné s cíli primárního umístění. Možnosti [replikace azure úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy) zahrnují:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) je sada funkcí určených pro analýzu velkých objemů dat, která je postavená na úložišti objektů Blob Azure. Omezení úložiště Azure a úložiště objektů blob platí pro úložiště datových jezer Gen2.

> [!NOTE]
> Společnost Microsoft doporučuje používat účet úložiště pro obecné účely v2 pro většinu scénářů. Můžete snadno upgradovat účet úložiště pro obecné účely v1 nebo Azure Blob na účet pro obecné účely v2 bez prostojů a bez nutnosti kopírování dat. Další informace naleznete v [tématu Upgrade na účet úložiště pro obecné účely v2](../articles/storage/common/storage-account-upgrade.md).

Pokud potřeby vaší aplikace překročí cíle škálovatelnosti jednoho účtu úložiště, můžete vytvořit aplikaci pro použití více účtů úložiště. Potom můžete rozdělit datové objekty mezi tyto účty úložiště. Informace o cenách objemu najdete v [tématu Ceny služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Všechny účty úložiště běží na topologii ploché sítě bez ohledu na to, kdy byly vytvořeny. Další informace o ploché síťové architektuře Azure Storage a škálovatelnosti najdete v [tématu Microsoft Azure Storage: Vysoce dostupná služba cloudového úložiště se silnou konzistencí](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
