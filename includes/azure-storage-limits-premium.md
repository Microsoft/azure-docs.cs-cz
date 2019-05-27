---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b75e24e5d59206ee8330c3ca9eaf86eacbad13d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114808"
---
### <a name="premium-performance-block-blob-storage"></a>Úložiště objektů blob bloku výkon úrovně Premium

Výkon bloku objektů blob v účtu služby premium storage je optimalizovaná pro aplikace, které používají menší, kilobajtů rozsah objektů. Je ideální pro aplikace, které vyžadují vysokou transakční sazby nebo konzistentní úložiště s nízkou latencí. Premium výkonu blokovou službou blob storage je navržená pro škálování s vašimi aplikacemi. Pokud plánujete nasadit tyto aplikace, které vyžadují stovky tisíc požadavků za sekundu nebo petabajty kapacitu úložiště, kontaktujte nás prosím odešlete v žádosti o podporu [webu Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage-preview"></a>Výkon úrovně Premium úložiště souborů (preview)

Soubory typu Premium, použijte účet jedinečný úložiště s názvem **úložiště souborů (preview)**. Tento typ účtu je navržená pro úlohy s vysokým vstupně-výstupních operací, vysoké propustnosti s konzistentní s nízkou latencí. File storage úrovně Premium se škáluje s velikosti zřízené sdílené složky.

|Oblast  |Cíl  |
|---------|---------|
|Maximální počet zřízení velikost     |100 TiB     |
|Sdílené položky   |Neomezené  |
|IOPS     |100 000    |
|Příchozí přenos|4,136 MiB/s     |
|Výchozí přenos|6,204 MiB/s |

 Premium soubor sdílet měřítko cíle, najdete v článku [soubory typu Premium škálování cíle](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets) oddílu.

### <a name="premium-performance-page-blob-storage"></a>Úložiště objektů blob stránky úrovně Premium výkonu

Výkon úrovně Premium, pro obecné účely v1 nebo v2 účty úložiště mají následující cíle škálovatelnosti:

| Celkový počet účtů kapacity                            | Celková šířka pásma pro účet místně redundantního úložiště                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Kapacita disku: 35 TB <br>Kapacita snímku: 10 TB | Až 50 gigabity za sekundu pro příchozí<sup>1</sup> + odchozí<sup>2</sup> |

<sup>1</sup> všechna data (požadavky) odesílané do účtu úložiště

<sup>2</sup> všechna data (požadavky), které byly přijaty z účtu úložiště

Pokud používáte účty úložiště úrovně premium výkonu pro nespravované disky a vaše aplikace překračuje cíle škálovatelnosti z jednoho účtu úložiště, může být vhodné k migraci na spravované disky. Pokud už nechcete migrovat do managed disks, sestavení aplikace pro použití více účtů úložiště. Potom data rozdělte mezi tyto účty úložiště. Například pokud chcete připojení disků 51 TB napříč několika virtuálními počítači, rozloženy je dva účty úložiště. 35 TB je limit pro účet úložiště jedné úrovně premium. Zkontrolujte, že účet úložiště premium jeden výkonu nikdy zajišťované disky větší než 35 TB.