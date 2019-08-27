---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d5ce4c094da3a411168c7fe4c282b15ceac7bb86
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036684"
---
Následující tabulka popisuje výchozí omezení pro účty úložiště Azure pro obecné účely V1, v2 a BLOB. Limit příchozího přenosu dat odkazuje na všechna data z požadavků, které se odesílají do účtu úložiště. Limit *odchozího* přenosu dat odkazuje na všechna data z odpovědí, které jsou přijaty z účtu úložiště.

| Resource | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště podle jednotlivých oblastí a předplatných, včetně účtů Standard a Premium | 250 |
| Maximální kapacita účtu úložiště | 2 PB pro USA a Evropu a 500 TB pro všechny ostatní oblasti (včetně Spojené království)<sup>1</sup>|
| Maximální počet kontejnerů objektů blob, objektů blob, sdílených složek, tabulek, front, entit nebo zpráv na účet úložiště | Bez omezení |
| Maximální počet požadavků<sup>1</sup> na účet úložiště | počet požadavků 20 000 za sekundu |
| Maximální příchozí<sup>1</sup> na účet úložiště (oblasti USA, Evropa) | 25 GB/s |
| Maximální příchozí<sup>1</sup> na účet úložiště (jiné oblasti než USA a Evropa) | 5 GB/s, pokud je povolená možnost RA-GRS/GRS, 10 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální výstup pro účty pro obecné účely v2 a BLOB Storage (všechny oblasti) | 50 GB/s |
| Maximální výstup pro účty úložiště V1 pro obecné účely (oblasti USA) | 20 GB/s, pokud je povolená možnost RA-GRS/GRS, 30 GB/s pro LRS/ZRS<sup>2</sup> |
| Maximální výstup pro účty úložiště pro obecné účely V1 (oblasti mimo USA) | 10 GB/s, pokud je povolená možnost RA-GRS/GRS, 15 GB/s pro LRS/ZRS<sup>2</sup> |

<sup>1</sup> Účty Azure úrovně Standard podporují větší limity kapacity a vyšší limity pro příchozí zpracování žádostí. Pokud chcete požádat o zvýšení limitů pro příchozí přenos dat, obraťte se na [podporu Azure](https://azure.microsoft.com/support/faq/). Další informace najdete v tématu [oznamujeme větší, vyšší škálování účtů úložiště](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>2</sup> [Azure Storage možnosti replikace](https://docs.microsoft.com/azure/storage/common/storage-redundancy) zahrnují:

- **RA-GRS**: Geograficky redundantní úložiště s přístupem pro čtení. Pokud je povolené RA-GRS, cíle odchozího přenosu pro sekundární umístění jsou stejné jako u primárního umístění.
- **GRS**: Geograficky redundantní úložiště.
- **ZRS**: Redundantní úložiště zóny
- **LRS**: Místně redundantní úložiště.

> [!NOTE]
> Pro většinu scénářů doporučujeme použít účet úložiště pro obecné účely v2. Účet Azure Blob Storage pro obecné účely v1 nebo Azure můžete snadno upgradovat na účet pro obecné účely v2 bez výpadků a bez nutnosti kopírovat data.
>
> Další informace o účtech Azure Storage najdete v tématu [Přehled účtu úložiště](../articles/storage/common/storage-account-overview.md).

Pokud požadavky vaší aplikace překračují cíle škálovatelnosti jednoho účtu úložiště, můžete vytvořit aplikaci pro používání více účtů úložiště. Datové objekty pak můžete rozdělit do oddílů v rámci těchto účtů úložiště. Informace o cenách na svazcích najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).

Všechny účty úložiště běží v ploché topologii sítě a podporují cíle škálovatelnosti a výkonu, které jsou uvedené v tomto článku, bez ohledu na to, kdy byly vytvořeny. Další informace o architektuře Azure Storage ploché sítě a o škálovatelnosti najdete v článku [Microsoft Azure Storage: Vysoce dostupná služba cloudového úložiště se silnými](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)konzistencí.

