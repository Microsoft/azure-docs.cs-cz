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
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371544"
---
Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje jiné funkce a má vlastní cenový model. Před vytvořením účtu úložiště zvažte následující rozdíly a určete, jaký typ účtu je pro vaše aplikace nejvhodnější. Typy účtů úložiště:

- **Účty úložiště pro obecné účely verze 2:** Základní typ účtu úložiště pro objekty blob, soubory, fronty a tabulky. Doporučuje se pro většinu scénářů s využitím služby Azure Storage.
- **Účty úložiště pro obecné účely verze 1:** Starší verze účtu pro objekty blob, soubory, fronty a tabulky. Pokud je to možné, používejte místo tohoto účtu účty úložiště pro obecné účely verze 2.
- **Účty úložiště objektů blob bloku:** Účty úložiště s charakteristikami výkonu úrovně Premium pro objekty blob bloku a doplňovací objekty blob. Doporučují se pro scénáře s vysokou rychlostí transakcí nebo scénáře, které využívají menší objekty nebo vyžadují konzistentně nízkou latenci úložiště.
- **Účty služby File Storage:** Účty úložiště pouze pro soubory s charakteristikami výkonu úrovně Premium. Doporučují se pro podnikové aplikace nebo aplikace s vysokým škálováním výkonu.
- **Účty služby Blob Storage:** Starší verze účtů úložiště pouze pro objekty blob. Pokud je to možné, používejte místo tohoto účtu účty úložiště pro obecné účely verze 2.

Následující tabulka popisuje jednotlivé typy účtů úložiště a jejich funkce:

| Typ účtu úložiště | Podporované služby                       | Podporované úrovně výkonu      | Podporované úrovně přístupu         | Možnosti replikace               | Model nasazení<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Šifrování<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Úložiště pro obecné účely verze 2   | Blob Storage, File Storage, Queue Storage, Table Storage, Disk Storage a Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Horká, studená a archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (Preview), RA-GZRS (Preview)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Šifrované              |
| Úložiště pro obecné účely verze 1   | Blob Storage, File Storage, Queue Storage, Table Storage a Disk Storage       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | –                            | LRS, GRS, RA-GRS                  | Resource Manager, Classic    | Šifrované              |
| Úložiště objektů blob bloku   | Blob Storage (pouze objekty blob bloku a doplňovací objekty blob) | Premium                       | –                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| File Storage   | Pouze File Storage | Premium                       | –                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| Blob Storage         | Blob Storage (pouze objekty blob bloku a doplňovací objekty blob) | Standard                      | Horká, studená a archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Šifrované              |

<div id="deployment-model"><sup>1</sup> Doporučujeme používat model nasazení Azure Resource Manager. V některých umístěních je stále možné vytvářet účty úložiště využívající model nasazení Classic a stávající účty Classic se dál podporují. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Modely nasazení Azure Resource Manager a Classic: Vysvětlení modelů nasazení a stavu prostředků</a>.</div><br/>

<div id="encryption"><sup>2</sup> Neaktivní uložená data ve všech účtech úložiště se šifrují pomocí Šifrování služby Storage (SSE). Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Šifrování služby Azure Storage pro neaktivní uložená data</a>.</div><br/>

<div id="archive"><sup>3</sup> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují pouze objekty blob bloku. Archivní úroveň je k dispozici pouze na úrovni jednotlivých objektů blob, a ne na úrovni účtu úložiště. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: Horká, studená a archivní úroveň úložiště</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> Zónově redundantní úložiště (ZRS) a geograficky zónově redundantní úložiště (GZRS nebo RA-GZRS) (Preview) jsou k dispozici pouze pro účty úložiště pro obecné účely verze 2, účty úložiště objektů blob bloku a účty služby File Storage v určitých oblastech. Další informace o možnostech redundance služby Azure Storage najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Redundance služby Azure Storage</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> Výkon úrovně Premium pro účty úložiště pro obecné účely verze 1 a 2 je k dispozici pouze pro objekty blob disku a objekty blob stránky. Výkon úrovně Premium pro objekty blob bloku a doplňovací objekty blob je k dispozici pouze u účtů úložiště objektů blob bloku. Výkon úrovně Premium pro soubory je k dispozici pouze u účtů služby File Storage.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzu velkých objemů dat založená na službě Azure Blob Storage. Data Lake Storage Gen2 se podporuje pouze u účtů úložiště pro obecné účely verze 2 s povoleným hierarchickým oborem názvů. Další informace o službě Data Lake Storage Gen2 najdete v <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">úvodu ke službě Azure Data Lake Storage Gen2</a>.</div>
