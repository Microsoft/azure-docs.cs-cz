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
ms.openlocfilehash: 280ef8550177a514a6704a8bfab226745222f91e
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029762"
---
Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Před vytvořením účtu úložiště zvažte tyto rozdíly a určete typ účtu, který je pro vaše aplikace nejvhodnější. Typy účtů úložiště jsou:

- **Účty pro obecné účely v2**: Základní typ účtu úložiště pro objekty blob, soubory, fronty a tabulky. Doporučuje se ve většině scénářů pomocí Azure Storage.
- **Účty pro obecné účely v1**: Starší typ účtu pro objekty blob, soubory, fronty a tabulky. Pokud je to možné, použijte místo toho účty pro obecné účely v2.
- **Účty úložiště objektů blob bloku**: Účty úložiště jen pro objekty BLOB s charakteristikami výkonu Premium Doporučuje se pro scénáře s vysokými sazbami za transakce, používání menších objektů nebo vyžadování trvalé latence úložiště.
- **Účty úložiště**úložiště: Jenom soubory účtů úložiště s charakteristikami výkonu Premium. Doporučuje se pro podnikové nebo vysoce výkonné škálování aplikace.
- **Účty úložiště BLOB**: Účty úložiště jen pro objekty blob Pokud je to možné, použijte místo toho účty pro obecné účely v2.

Následující tabulka popisuje typy účtů úložiště a jejich možnosti:

| Typ účtu úložiště | Podporované služby                       | Podporované úrovně výkonu      | Podporované úrovně přístupu         | Možnosti replikace               | Model nasazení<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Šifrování<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Obecné účely v2   | Objekt blob, soubor, fronta, tabulka a disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, ZGRS (Preview), RA-ZGRS (Preview)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Zašifrováno              |
| Obecné účely v1   | Objekt blob, soubor, fronta, tabulka a disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Není k dispozici                            | LRS, GRS, RA-GRS                  | Správce prostředků, klasický    | Zašifrováno              |
| Úložiště objektů blob bloku   | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | Premium                       | Není k dispozici                            | LRS                               | Resource Manager             | Zašifrováno              |
| Úložiště   | Pouze soubory | Premium                       | Není k dispozici                            | LRS                               | Resource Manager             | Zašifrováno              |
| Blob Storage         | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | Standard                      | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Zašifrováno              |

<div id="deployment-model"><sup>1</sup> Doporučuje se použít model nasazení Azure Resource Manager. Účty úložiště, které používají model nasazení Classic, se stále dají vytvořit v některých umístěních a stávající klasické účty se pořád podporují. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. Classic Deployment: Pochopení modelů nasazení a stavu vašich prostředků</a>.</div>

<div id="encryption"><sup>2</sup> . Všechny účty úložiště se šifrují pomocí Šifrování služby Storage (SSE) pro neaktivní neaktivní data. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">šifrování služby Azure Storage pro</a>neaktivní neaktivní data.</div>

<div id="archive"><sup>3</sup> . Archivní úroveň je k dispozici pouze na úrovni jednotlivých objektů blob, nikoli na úrovni účtu úložiště. Archivovat lze pouze objekty blob bloku a doplňovací objekty blob. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">úložiště objektů BLOB v Azure: Horké, studené a archivní úrovně</a>úložiště.</div>

<div id="zone-redundant-storage"><sup>4</sup> . Redundantní úložiště (ZRS) a Geografická zóna redundantní úložiště (GZRS) (Preview) jsou k dispozici pouze pro účty úložiště standard pro obecné účely verze 2. Další informace o ZRS najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">redundantní úložiště zóny (ZRS): Vysoce dostupné Azure Storage aplikace</a>. Další informace o GZRS najdete v článku <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">geograficky redundantní úložiště pro vysokou dostupnost a maximální trvanlivost (Preview)</a>. Další informace o dalších možnostech replikace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">replikace Azure Storage</a>.</div>

<div id="premium-performance"><sup>5</sup> . K dispozici je výkon úrovně Premium pro účty pro obecné účely v2 a obecné účely v1 jenom pro objekty blob disku a stránky.</div>
