---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b8a72640ebe8daffedb196456df7d40bc41b7b1b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557303"
---
Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Před vytvořením účtu úložiště zvažte tyto rozdíly a určete typ účtu, který je pro vaše aplikace nejvhodnější. Typy účtů úložiště jsou:

- **Účty pro obecné účely v2**: základní typ účtu úložiště pro objekty blob, soubory, fronty a tabulky. Doporučuje se ve většině scénářů pomocí Azure Storage.
- **Účty pro obecné účely v1**: starší typ účtu pro objekty blob, soubory, fronty a tabulky. Pokud je to možné, použijte místo toho účty pro obecné účely v2.
- **Účty BlockBlobStorage**: účty úložiště s charakteristikou výkonu Premium pro objekty blob bloku a doplňovací objekty blob. Doporučuje se u scénářů s vysokými sazbami transakcí nebo scénářů, které používají menší objekty nebo vyžadují konzistentně nízkou latenci úložiště.
- **Účty úložiště** souborů: jenom účty úložiště s charakteristikami výkonu Premium. Doporučuje se pro podnikové nebo vysoce výkonné škálování aplikace.
- **Účty BlobStorage**: starší účty úložiště jen pro objekty blob. Pokud je to možné, použijte místo toho účty pro obecné účely v2.

Následující tabulka popisuje typy účtů úložiště a jejich možnosti:

| Typ účtu úložiště | Podporované služby                       | Podporované úrovně výkonu      | Podporované úrovně přístupu         | Možnosti replikace               | Model nasazení<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Šifrování<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Obecné účely v2   | Objekt blob, soubor, fronta, tabulka, disk a Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (Preview), RA-GZRS (Preview)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Šifrované              |
| Obecné účely v1   | Objekt blob, soubor, fronta, tabulka a disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Není k dispozici                            | LRS, GRS, RA-GRS                  | Správce prostředků, klasický    | Šifrované              |
| BlockBlobStorage   | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | Premium                       | Není k dispozici                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| Úložiště   | Pouze soubor | Premium                       | Není k dispozici                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| Blob Storage         | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | Standard                      | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Šifrované              |

<div id="deployment-model"><sup>1</sup> Doporučuje se použít model nasazení Azure Resource Manager. Účty úložiště, které používají model nasazení Classic, se stále dají vytvořit v některých umístěních a stávající klasické účty se pořád podporují. Další informace najdete v tématu <a href="/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. Classic Deployment: Principy modelů nasazení a stavu vašich prostředků</a>.</div><br/>

<div id="encryption"><sup>2</sup> . Všechny účty úložiště se šifrují pomocí Šifrování služby Storage (SSE) pro neaktivní neaktivní data. Další informace najdete v tématu <a href="/azure/storage/common/storage-service-encryption">šifrování služby Azure Storage pro</a>neaktivní neaktivní data.</div><br/>

<div id="archive"><sup>3</sup> archivní úložiště a vrstvení na úrovni objektů BLOB podporují jenom objekty blob bloku. Archivní úroveň je k dispozici pouze na úrovni jednotlivých objektů blob, nikoli na úrovni účtu úložiště. Další informace najdete v tématu <a href="/azure/storage/blobs/storage-blob-storage-tiers">úložiště objektů BLOB v Azure: horká, studená a archivní úroveň úložiště</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> . Redundantní úložiště (ZRS) a Geografická zóna redundantní úložiště (GZRS/RA-GZRS) (verze Preview) jsou k dispozici pouze pro účty standard pro obecné účely v2, BlockBlobStorage a úložiště v určitých oblastech. Další informace o možnostech redundance Azure Storage najdete v tématu <a href="/azure/storage/common/storage-redundancy">Azure Storage redundance</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> . K dispozici je výkon úrovně Premium pro účty pro obecné účely v2 a obecné účely v1 jenom pro objekty blob disku a stránky. Výkon služby Premium pro objekty blob bloku nebo Append je k dispozici pouze v účtech BlockBlobStorage. Premium Performance for Files je k dispozici pouze v účtech úložiště.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na službě Azure Blob Storage. Data Lake Storage Gen2 se podporuje jenom pro účty úložiště pro obecné účely v2 s povoleným hierarchickým oborem názvů. Další informace o Data Lake Storage Gen2 najdete v tématu <a href="/azure/storage/blobs/data-lake-storage-introduction">Úvod do Azure Data Lake Storage Gen2</a>.</div>