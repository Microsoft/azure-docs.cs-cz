---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ee9b70827c19236287f81f66f4b9c6e1004a54fd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114507"
---
Následující tabulka popisuje výchozí limity pro Azure pro obecné účely v1, v2 a účty Blob storage. *Příchozího přenosu dat* limit odkazuje na všechna data z požadavků, které se odesílají na účet úložiště. *Odchozího přenosu dat* limit odkazuje na všechna data z odpovědi, které byly přijaty z účtu úložiště.

| Resource | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště na oblast a předplatné, včetně účtů standard a premium | 250 |
| Maximální kapacita účtu | 2 PB pro USA a Evropě, 500 TB pro všechny ostatní oblasti, která zahrnuje Spojeném království |
| Maximální počet kontejnerů objektů blob, objekty BLOB, sdílené složky, tabulky, fronty, entity nebo zpráv za účet úložiště | Bez omezení |
| Maximální frekvence požadavků<sup>1</sup> jeden účet úložiště | 20 000 požadavků za sekundu |
| Maximální příchozí přenos dat<sup>1</sup> jeden účet úložiště (oblastem v USA) | 10 GB/s Pokud je povolené RA-GRS/GRS, 20 GB/s pro LRS nebo ZRS<sup>2</sup> |
| Maximální příchozí přenos dat<sup>1</sup> jeden účet úložiště (oblasti mimo USA) | 5 GB/s Pokud je povolené RA-GRS/GRS, 10 GB/s pro LRS nebo ZRS<sup>2</sup> |
| Maximální odchozího přenosu dat pro obecné účely v2 a účty Blob storage (všechny oblasti) | 50 Gbps |
| Maximální odchozího přenosu dat pro účty úložiště pro obecné účely v1 (oblastem v USA) | 20 GB/s Pokud je povolené RA-GRS/GRS, 30 GB/s pro LRS nebo ZRS<sup>2</sup> |
| Maximální odchozího přenosu dat pro účty úložiště pro obecné účely v1 (oblasti mimo USA) | 10 GB/s Pokud je povolené RA-GRS/GRS, 15 GB/s pro LRS nebo ZRS<sup>2</sup> |

<sup>1</sup>účty úložiště azure úrovně Standard podporovat vyšší limity pro příchozí přenos dat podle požadavku. Chcete-li požádat o zvýšení v omezení účtu pro příchozí přenos dat, obraťte se na [podpory Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [replikace azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) mezi možnosti patří:
* **RA-GRS**: Geograficky redundantní úložiště jen pro čtení. Pokud je povolené RA-GRS, jsou stejné jako pro primární umístění cíle odchozího přenosu dat pro sekundární lokality.
* **GRS**: Geograficky redundantní úložiště. 
* **ZRS**: Zónově redundantní úložiště.
* **LRS**: Místně redundantní úložiště. 

> [!NOTE]
> Doporučujeme používat účet úložiště pro obecné účely verze 2 pro většinu scénářů. Pro obecné účely v1 nebo účet úložiště objektů Blob v Azure můžete snadno upgradovat na účet pro obecné účely v2 došlo k výpadku a bez nutnosti kopírovat data.
>
> Další informace o účtech Azure Storage najdete v tématu [účet úložiště – přehled](../articles/storage/common/storage-account-overview.md). 

Potřeb svojí aplikace překročení cíle škálovatelnosti z jednoho účtu úložiště, můžete vytvářet aplikace pomocí více účtů úložiště. Pak můžete dělit datové objekty mezi těmito účty úložiště. Informace o cenách najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Všechny účty úložiště spustit na ploché síťové topologie a podporu škálovatelnost a výkonnostní cíle, které jsou popsané v tomto článku, bez ohledu na to, když byly vytvořeny. Další informace o architektuře ploché sítě služby Azure Storage a na škálovatelnost, naleznete v tématu [Microsoft Azure Storage: Služby s vysokou dostupností cloudového úložiště se silnou konzistenci](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

