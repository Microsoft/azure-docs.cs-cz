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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371544"
---
Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Zvažte tyto rozdíly před vytvořením účtu úložiště k určení typu účtu, který je nejlepší pro vaše aplikace. Typy účtů úložiště jsou:

- **Účty pro obecné účely v2**: Typ účtu základního úložiště pro objekty BLOB, soubory, fronty a tabulky. Doporučeno pro většinu scénářů pomocí Služby Azure Storage.
- **Účty pro obecné účely v1**: Starší typ účtu pro objekty BLOB, soubory, fronty a tabulky. Místo toho použijte účty pro obecné účely v2, pokud je to možné.
- **BlockBlobStorage účty**: Účty úložiště s vlastnostmi výkonu premium pro objekty BLOB bloku a připojit objekty BLOB. Doporučeno pro scénáře s vysokými mírami transakcí nebo scénáře, které používají menší objekty nebo vyžadují konzistentně nízkou latenci úložiště.
- **Účty FileStorage**: Účty úložiště pouze pro soubory s vlastnostmi výkonu premium. Doporučeno pro podnikové nebo vysoce výkonné škálovací aplikace.
- **Účty BlobStorage**: Starší účty úložiště pouze pro objekty blob. Místo toho použijte účty pro obecné účely v2, pokud je to možné.

Následující tabulka popisuje typy účtů úložiště a jejich možnosti:

| Typ účtu úložiště | Podporované služby                       | Podporované úrovně výkonu      | Podporované úrovně přístupu         | Možnosti replikace               | Model nasazení<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Šifrování<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Pro všeobecné účely V2   | Objekt blob, soubor, fronta, tabulka, disk a datové jezero Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standardní, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Horké, Cool, Archiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (náhled), RA-GZRS (náhled)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Šifrované              |
| Pro všeobecné účely V1   | Objekt blob, soubor, fronta, tabulka a disk       | Standardní, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Není dostupné.                            | LRS, GRS, RA-GRS                  | Správce zdrojů, Classic    | Šifrované              |
| Úložiště BlockBlobStorage   | Objekt Blob (pouze objekty BLOB bloku a objekty BLOB příloh) | Premium                       | Není dostupné.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| Úložiště souborů   | Pouze soubor | Premium                       | Není dostupné.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Šifrované              |
| Úložiště objektů BlobStorage         | Objekt Blob (pouze objekty BLOB bloku a objekty BLOB příloh) | Standard                      | Horké, Cool, Archiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Šifrované              |

<div id="deployment-model"><sup>1.</sup> Doporučujeme použít model nasazení Azure Resource Manageru. Účty úložiště pomocí modelu klasického nasazení lze stále vytvořit v některých umístěních a existující klasické účty jsou i nadále podporovány. Další informace najdete v <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">tématu Azure Resource Manager vs. klasické nasazení: Principy modelů nasazení a stavu vašich prostředků</a>.</div><br/>

<div id="encryption"><sup>2.</sup> Všechny účty úložiště jsou šifrovány pomocí šifrování služby úložiště (SSE) pro data v klidovém stavu. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Šifrování služby Úložiště Azure pro data v klidovém stavu</a>.</div><br/>

<div id="archive"><sup>3</sup> Archivní úložiště a vrstvení na úrovni objektů blob podporují pouze objekty BLOB bloku. Úroveň Archivu je k dispozici pouze na úrovni jednotlivého objektu blob, nikoli na úrovni účtu úložiště. Další informace najdete v <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">tématu úložiště objektů blob Azure: horké, studené a archivní úrovně úložiště</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4.</sup> Zónově redundantní úložiště (ZRS) a geograficky zónově redundantní úložiště (GZRS/RA-GZRS) (preview) jsou k dispozici pouze pro standardní účty V2, BlockBlobStorage a FileStorage v určitých oblastech. Další informace o možnostech redundance azure storage najdete v <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">tématu redundance Azure Storage</a>.</div><br/>

<div id="premium-performance"><sup>5.</sup> Výkon premium pro obecné účely v2 a obecné účely v1 účty je k dispozici pouze pro disk a stránku objektblob. Výkon premium pro objekty BLOB bloku nebo připojení jsou k dispozici pouze na účtech BlockBlobStorage. Výkon premium pro soubory jsou k dispozici pouze na účty FileStorage.</div><br/>

<div id="data-lake-gen2"><sup>6.</sup> Azure Data Lake Storage Gen2 je sada funkcí určených pro analýzu velkých objemů dat, která je postavená na úložišti objektů blob Azure. Data Lake Storage Gen2 je podporována pouze na účty úložiště pro obecné účely V2 s povolenou hierarchickou oborovou značkou. Další informace o úložišti datových jezer Gen2 najdete <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">v tématu Úvod do Azure Data Lake Storage Gen2</a>.</div>
