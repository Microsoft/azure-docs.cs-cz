---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706027"
---
Tady je seznam podporovaných účtů úložiště a typů úložiště pro Data Box zařízení. Úplný seznam všech možností pro všechny typy účtů úložiště najdete v tématu [typy účtů úložiště](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Pro příkazy import v následující tabulce jsou uvedeny podporované účty úložiště.

| **Účet úložiště/podporované typy úložišť** | **Objekt blob bloku** |**Objekt blob stránky** _ |_ *Soubory Azure** |**Poznámky**|
| --- | --- | -- | -- | -- |
| Klasický Standard | Y | Y | Y |
| Obecné účely v1 Standard  | Y | Y | Y | Podporují horkou a studenou.|
| Obecné účely v1 Premium  |  | Y| | |
| Obecné pro účely v2 Standard  | Y | Y | Y | Podporují horkou a studenou.|
| Pro obecné účely v2 Premium  |  |Y | | |
| Úložiště Azure Premium |  |  | Y |  |  
| Úložiště objektů BLOB Standard |Y | | |Podporují horkou a studenou. |

\**-Data nahraná do objektů blob stránky musí mít 512 bajtů (například VHD).*

V případě exportních objednávek následující tabulka zobrazuje podporované účty úložiště.

| **Účet úložiště/podporované typy úložišť** | **Objekt blob bloku** |**Objekt blob stránky** _ |_ *Soubory Azure** |**Podporované úrovně přístupu**|
| --- | --- | -- | -- | -- |
| Klasický Standard | Y | Y | Y | |
| Obecné účely v1 Standard  | Y | Y | Y | Horká, studená|
| Obecné účely v1 Premium  |  | Y| | |
| Obecné pro účely v2 Standard  | Y | Y | Y | Horká, studená|
| Pro obecné účely v2 Premium  |  |Y | | |
| Úložiště Azure Premium |  |  | Y |  |
| Úložiště objektů BLOB Standard |Y | | |Horká, studená |
| Úložiště objektů blob bloku Premium |Y | | |Horká, studená |
| Úložiště objektů blob stránky Premium | |Y | | |

> [!IMPORTANT]
> - Pro účty pro obecné účely Data Box nepodporuje typy úložiště front, tabulek a disků pro příkazy import. Pro exportní objednávky Data Box pro účty pro obecné účely nepodporuje typy úložiště Queue, Table, disk a Azure Data Lake Gen 2.
> - Data Box nepodporuje přidávání objektů BLOB pro Blob Storage a blokování účtů Blob Storage.
> - Podpora protokolů systému souborů NFS (Network File System) 3,0 ve službě Azure Blob Storage není podporována Data Box.
> - Data nahraná do objektů blob stránky musí mít 512 bajtů (například VHD).
> - Exportovat lze maximálně 80 TB.
> - Historie souborů a snímky objektů BLOB se neexportují.