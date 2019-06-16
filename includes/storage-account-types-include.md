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
ms.openlocfilehash: d96e69fb526cff633c78e9ac8a1679762014cd4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133663"
---
Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má vlastní cenový model. Než vytvoříte účet úložiště a určit typ účtu, který je nejvhodnější pro vaše aplikace, zvažte následující rozdíly. Typy účtů úložiště jsou:

- **Účty pro obecné účely v2**: Typ účtu storage úrovně Basic pro objekty BLOB, soubory, fronty a tabulky. Doporučujeme pro většinu scénářů použití služby Azure Storage.
- **Účty pro obecné účely v1**: Typ starší verze účtu pro objekty BLOB, soubory, fronty a tabulky. Místo toho použijte účty pro obecné účely v2, pokud je to možné.
- **Blokovat účty úložiště blob**: Účty úložiště pouze objektů BLOB s charakteristikami výkonu premium. Doporučuje pro scénáře s vysokou transakce plateb, použití menší objektů nebo vyžadování úložiště konzistentně s nízkou latencí.
- **Úložiště souborů (preview) účty úložiště**: Účty úložiště pouze soubory s charakteristikami výkonu premium. Doporučuje se pro organizace nebo škálování aplikace s vysokým výkonem.
- **Účty BLOB storage**: Účty úložiště pouze objektů BLOB. Místo toho použijte účty pro obecné účely v2, pokud je to možné.

Následující tabulka popisuje typy účtů úložiště a jejich funkce:

| Typ účtu úložiště | Podporované služby                       | Úrovně výkonu podporované      | Podporované přístupu         | Možnosti replikace               | Model nasazení<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Šifrování<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Pro obecné účely V2   | Objekt BLOB, soubor, fronty, tabulky a Disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Šifrované              |
| Pro obecné účely V1   | Objekt BLOB, soubor, fronty, tabulky a Disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | neuvedeno                            | LRS, GRS, RA-GRS                  | Resource Manager, Classic    | Šifrované              |
| Úložiště objektů blob bloku   | Objekt BLOB (objekty BLOB bloku a doplňovacích objektů BLOB jenom) | Premium                       | neuvedeno                            | LRS                               | Resource Manager             | Šifrované              |
| Úložiště souborů (preview)   | Pouze pro soubory | Premium                       | neuvedeno                            | LRS                               | Resource Manager             | Šifrované              |
| Blob Storage         | Objekt BLOB (objekty BLOB bloku a doplňovacích objektů BLOB jenom) | Standard                      | Horká, studená, archivní<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Šifrované              |

<div id="deployment-model"><sup>1</sup>doporučuje se použít model nasazení Azure Resource Manageru. Účty úložiště pomocí modelu nasazení classic je možné vytvořit i nadále v některých umístěních, a nadále podporovat existující klasické účty. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manageru a klasického nasazení: Vysvětlení modelů nasazení a stavu prostředků</a>.</div>

<div id="encryption"><sup>2</sup>všechny účty úložiště jsou šifrované pomocí šifrování služby Storage (SSE) pro neaktivní uložená data. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">šifrování služby Azure Storage pro neaktivní uložená Data</a>.</div>

<div id="archive"><sup>3</sup>archivní úrovni není k dispozici na úrovni pouze jednotlivých objektů blob na úrovni účtu úložiště. Pouze objekty BLOB bloku a doplňovacích objektů blob je možné archivovat. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">úložiště objektů Blob v Azure: Horké, studené a archivní úroveň úložiště</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup>zónově redundantní úložiště (ZRS) je k dispozici pouze pro účty úložiště standard pro obecné účely v2. Další informace o ZRS, naleznete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">zónově redundantní úložiště (ZRS): Vysoce dostupné aplikace služby Azure Storage</a>. Další informace o dalších možnostech replikace najdete v tématu <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">replikace Azure Storage</a>.</div>

<div id="premium-performance"><sup>5</sup>výkon úrovně premium pro obecné účely v2 a účty pro obecné účely v1 je k dispozici pro disk a stránky blob.</div>
