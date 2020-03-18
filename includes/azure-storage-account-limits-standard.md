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
ms.openlocfilehash: b5de45086b324006a3dafdef5c80689923650759
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79382184"
---
Následující tabulka popisuje výchozí limity pro účty úložiště Azure pro obecné účely verze 1 a 2, účty služby Blob Storage, účty úložiště objektů blob bloku a účty úložiště s povolenou službou Data Lake Storage Gen2. Limit *příchozího přenosu dat* odkazuje na všechna data odesílaná do účtu úložiště. Limit *výchozího přenosu dat* odkazuje na všechna data přijímaná z účtu úložiště.

| Prostředek | Výchozí omezení |
| --- | --- |
| Počet účtů úložiště na oblast a předplatné, včetně účtů úložiště úrovně Standard a Premium a účtů úložiště s povolenou službou Data Lake Storage Gen2.<sup>3</sup> | 250 |
| Maximální kapacita účtu úložiště | 5 PiB<sup>1</sup>|
| Maximální počet kontejnerů objektů blob, objektů blob, sdílených složek, tabulek, front, entit nebo zpráv na účet úložiště | Bez omezení |
| Maximální frekvence požadavků<sup>1</sup> na účet úložiště | 20 000 požadavků za sekundu |
| Maximální příchozí přenos dat<sup>1</sup> na účet úložiště (oblasti USA a Evropy) | 25 Gb/s |
| Maximální příchozí přenos dat<sup>1</sup> na účet úložiště (oblasti mimo USA a Evropu) | 5 Gb/s, pokud je povolené úložiště RA-GRS nebo GRS, 10 Gb/s v případě úložiště LRS a ZRS<sup>2</sup> |
| Maximální výchozí přenos dat pro účty úložiště pro obecné účely verze 2 a účty služby Blob Storage (všechny oblasti) | 50 Gb/s |
| Maximální výchozí přenos dat pro účty úložiště pro obecné účely verze 1 (oblasti USA) | 20 Gb/s, pokud je povolené úložiště RA-GRS nebo GRS, 30 Gb/s v případě úložiště LRS a ZRS<sup>2</sup> |
| Maximální výchozí přenos dat pro účty úložiště pro obecné účely verze 1 (oblasti mimo USA) | 10 Gb/s, pokud je povolené úložiště RA-GRS nebo GRS, 15 Gb/s v případě úložiště LRS a ZRS<sup>2</sup> |
| Maximální počet pravidel virtuální sítě na účet úložiště | 200 |
| Maximální počet pravidel IP adres na účet úložiště | 200 |

<sup>1</sup> Účty služby Azure Storage úrovně Standard na vyžádání podporují vyšší limity kapacity a příchozího přenosu dat. Pokud chcete požádat o navýšení limitů účtu, kontaktujte [podporu Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Pokud je pro váš účet úložiště povolené geograficky redundantní úložiště jen pro čtení (RA-GRS) nebo geograficky zónově redundantní úložiště jen pro čtení (RA-GZRS), cíle výchozího přenosu dat pro sekundární oblast jsou stejné jako pro primární oblast. Mezi možnosti [replikace služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) patří:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) je sada funkcí vyhrazených pro analýzu velkých objemů dat založená na službě Azure Blob Storage. Na Data Lake Storage Gen2 se vztahují omezení služeb Azure Storage a Blob Storage.

> [!NOTE]
> Microsoft doporučuje pro většinu scénářů používat účet úložiště pro obecné účely verze 2. Účet úložiště pro obecné účely verze 1 nebo účet služby Azure Blob Storage můžete snadno upgradovat na účet úložiště pro obecné účely verze 2, a to bez výpadků a nutnosti kopírovat data. Další informace najdete v tématu [Upgrade na účet úložiště pro obecné účely verze 2](../articles/storage/common/storage-account-upgrade.md).

Pokud požadavky vaší aplikace překročí cíle škálovatelnosti jednoho účtu úložiště, můžete aplikaci sestavit tak, aby využívala více účtů úložiště. Datové objekty pak můžete rozdělit mezi tyto účty úložiště. Informace o cenách najdete v článku [Ceny služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Všechny účty úložiště běží v ploché síťové topologii bez ohledu na to, kde se vytvořily. Další informace o ploché architektuře sítě služby Azure Storage a škálovatelnosti najdete v tématu [Microsoft Azure Storage: Vysoce dostupná služba cloudového úložiště se silnou konzistencí](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
