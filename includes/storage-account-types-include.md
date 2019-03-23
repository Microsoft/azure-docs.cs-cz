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
ms.openlocfilehash: bffe948eec81b480e51d0cf5a25f6091f397dd15
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372846"
---
Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má vlastní cenový model. Než vytvoříte účet úložiště a určit typ účtu, který je nejvhodnější pro vaše aplikace, zvažte následující rozdíly. Typy účtů úložiště jsou:

- **Účty pro obecné účely v2**: Typ účtu storage úrovně Basic pro objekty BLOB, soubory, fronty a tabulky. Doporučujeme pro většinu scénářů použití služby Azure Storage.
- **Účty pro obecné účely v1**: Typ starší verze účtu pro objekty BLOB, soubory, fronty a tabulky. Místo toho použijte účty pro obecné účely v2, pokud je to možné.
- **Blokovat účty úložiště blob**: Účty úložiště pouze objektů BLOB s charakteristikami výkonu premium. Doporučuje pro scénáře s vysokou transakce plateb, použití menší objektů nebo vyžadování úložiště konzistentně s nízkou latencí.
- **Účty BLOB storage**: Účty úložiště pouze objektů BLOB. Místo toho použijte účty pro obecné účely v2, pokud je to možné.

Následující tabulka popisuje typy účtů úložiště a jejich funkce:

| Typ účtu úložiště | Podporované služby                       | Úrovně výkonu podporované      | Podporované přístupu         | Možnosti replikace               | Model nasazení<sup>1</sup> | Šifrování<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Pro obecné účely V2   | Objekt BLOB, soubor, fronty, tabulky a Disk       | Standard, Premium<sup>5</sup> | Horká, studená, archivní<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager             | Šifrované              |
| Pro obecné účely V1   | Objekt BLOB, soubor, fronty, tabulky a Disk       | Standard, Premium<sup>5</sup> | neuvedeno                            | LRS, GRS, RA-GRS                  | Resource Manager, Classic    | Šifrované              |
| Úložiště objektů blob bloku   | Objekt BLOB (objekty BLOB bloku a doplňovacích objektů BLOB jenom) | Premium                       | neuvedeno                            | LRS                               | Resource Manager             | Šifrované              |
| Blob Storage         | Objekt BLOB (objekty BLOB bloku a doplňovacích objektů BLOB jenom) | Standard                      | Horká, studená, archivní<sup>3</sup> | LRS, GRS, RA-GRS                  | Resource Manager             | Šifrované              |

<sup>1</sup>doporučuje se použít model nasazení Azure Resource Manageru. Účty úložiště pomocí modelu nasazení classic je možné vytvořit i nadále v některých umístěních, a nadále podporovat existující klasické účty. Další informace najdete v tématu [Azure Resource Manageru a klasického nasazení: Vysvětlení modelů nasazení a stavu prostředků](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>všechny účty úložiště jsou šifrované pomocí šifrování služby Storage (SSE) pro neaktivní uložená data. Další informace najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená Data](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>archivní úrovni není k dispozici na úrovni pouze jednotlivých objektů blob na úrovni účtu úložiště. Pouze objekty BLOB bloku a doplňovacích objektů blob je možné archivovat. Další informace najdete v tématu [úložiště objektů Blob v Azure: Horké, studené a archivní úroveň úložiště](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zónově redundantní úložiště (ZRS) je k dispozici pouze pro účty úložiště standard pro obecné účely v2. Další informace o ZRS, naleznete v tématu [zónově redundantní úložiště (ZRS): Vysoce dostupné aplikace služby Azure Storage](../articles/storage/common/storage-redundancy-zrs.md). Další informace o dalších možnostech replikace najdete v tématu [replikace Azure Storage](../articles/storage/common/storage-redundancy.md).

<sup>5</sup> výkon úrovně premium pro obecné účely v2 a účty pro obecné účely v1 je k dispozici pro disk a stránky blob.