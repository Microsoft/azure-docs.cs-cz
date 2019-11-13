---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 1a9cebe334b266dfcf7f06608fd6a526ded7179b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73988830"
---
Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Před vytvořením účtu úložiště zvažte tyto rozdíly a určete typ účtu, který je pro vaše aplikace nejvhodnější. Typy účtů úložiště jsou:

- **Účty pro obecné účely v2**: základní typ účtu úložiště pro objekty blob, soubory, fronty a tabulky. Doporučuje se ve většině scénářů pomocí Azure Storage.
- **Účty pro obecné účely v1**: starší typ účtu pro objekty blob, soubory, fronty a tabulky. Pokud je to možné, použijte místo toho účty pro obecné účely v2.
- **Účty BlockBlobStorage**: účty úložiště jen pro objekty BLOB s charakteristikami výkonu Premium. Doporučuje se pro scénáře s vysokými sazbami za transakce, používání menších objektů nebo vyžadování trvalé latence úložiště.
- **Účty úložiště**souborů: jenom účty úložiště s charakteristikami výkonu Premium. Doporučuje se pro podnikové nebo vysoce výkonné škálování aplikace.
- **Účty BlobStorage**: starší účty úložiště jen pro objekty blob. Pokud je to možné, použijte místo toho účty pro obecné účely v2.

Následující tabulka popisuje typy účtů úložiště a jejich možnosti:

| Typ účtu úložiště | Podporované služby                       | Podporované úrovně výkonu      | Podporované úrovně přístupu         | Možnosti replikace               | Model nasazení<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Šifrování<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Obecné účely v2   | Objekt blob, soubor, fronta, tabulka a disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (Preview), RA-GZRS (Preview)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Šifrované              |
| Obecné účely v1   | Objekt blob, soubor, fronta, tabulka a disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | neuvedeno                            | LRS, GRS, RA-GRS                  | Správce prostředků, klasický    | Šifrované              |
| BlockBlobStorage   | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | Premium                       | neuvedeno                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| Úložiště   | Pouze soubory | Premium                       | neuvedeno                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| BlobStorage         | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | Standard                      | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Šifrované              |

<div id="deployment-model"><sup>1</sup> Doporučuje se použít model nasazení Azure Resource Manager. Účty úložiště, které používají model nasazení Classic, se stále dají vytvořit v některých umístěních a stávající klasické účty se pořád podporují. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. Classic Deployment: Principy modelů nasazení a stavu vašich prostředků</a>.</div>

<div id="encryption"><sup>2</sup> . Všechny účty úložiště se šifrují pomocí Šifrování služby Storage (SSE) pro neaktivní neaktivní data. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">šifrování služby Azure Storage pro</a>neaktivní neaktivní data.</div>

<div id="archive"><sup>3</sup> . Archivní úroveň je k dispozici pouze na úrovni jednotlivých objektů blob, nikoli na úrovni účtu úložiště. Archivovat lze pouze objekty blob bloku a doplňovací objekty blob. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">úložiště objektů BLOB v Azure: horká, studená a archivní úroveň úložiště</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> . Redundantní úložiště (ZRS) a Geografická zóna redundantní úložiště (GZRS/RA-GZRS) (verze Preview) jsou k dispozici pouze pro účty standard pro obecné účely v2, BlockBlobStorage a úložiště v určitých oblastech. Další informace o ZRS najdete v článku <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">redundantní úložiště (ZRS): vysoce dostupné aplikace Azure Storage</a>. Další informace o GZRS/RA-GZRS najdete v tématu o <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">vysoké dostupnosti a maximální trvanlivosti (Preview) v geograficky zóně redundantním úložišti</a>. Další informace o dalších možnostech replikace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">replikace Azure Storage</a>.</div>

<div id="premium-performance"><sup>5</sup> . K dispozici je výkon úrovně Premium pro účty pro obecné účely v2 a obecné účely v1 jenom pro objekty blob disku a stránky.</div>
