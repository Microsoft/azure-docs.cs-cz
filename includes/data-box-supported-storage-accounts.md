---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200305"
---
Tady je seznam podporovaných účtů úložiště a typů úložiště pro Data Box zařízení. Úplný seznam všech různých typů účtů úložiště a jejich úplných možností najdete v tématu [typy účtů úložiště](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

Pro příkazy import v následující tabulce jsou uvedeny podporované účty úložiště.

| **Účet úložiště/podporované typy úložišť** | **Objekt blob bloku** |**Objekt blob stránky*** |**Soubory Azure** |**Poznámky**|
| --- | --- | -- | -- | -- |
| Klasický Standard | A | A | A |
| Obecné účely v1 Standard  | A | A | A | Podporují horkou a studenou.|
| Obecné účely v1 Premium  |  | A| | |
| Obecné pro účely v2 Standard  | A | A | A | Podporují horkou a studenou.|
| Pro obecné účely v2 Premium  |  |A | | |
| Úložiště objektů BLOB Standard |A | | |Podporují horkou a studenou. |

\**-Data nahraná do objektů blob stránky musí mít 512 bajtů (například VHD).*

V případě exportních objednávek následující tabulka zobrazuje podporované účty úložiště.

| **Účet úložiště/podporované typy úložišť** | **Objekt blob bloku** |**Objekt blob stránky*** |**Soubory Azure** |**Podporované úrovně přístupu**|
| --- | --- | -- | -- | -- |
| Klasický Standard | A | A | A | |
| Obecné účely v1 Standard  | A | A | A | Horká, studená|
| Obecné účely v1 Premium  |  | A| | |
| Obecné pro účely v2 Standard  | A | A | A | Horká, studená|
| Pro obecné účely v2 Premium  |  |A | | |
| Úložiště objektů BLOB Standard |A | | |Horká, studená |
| Úložiště objektů blob bloku Premium |A | | |Horká, studená |
| Úložiště objektů blob stránky Premium | |A | | |

> [!IMPORTANT]
> - U účtů pro obecné účely Data Box nepodporuje typy úložiště ve frontě, tabulkách, discích a Azure Data Lakech 2. generace.
> - Data Box nepodporuje přidávání objektů BLOB pro Blob Storage a blokování účtů Blob Storage.
> - Data Box nepodporuje účty Premium File Storage.
> - Data nahraná do objektů blob stránky musí mít 512 bajtů (například VHD).
> - Exportovat lze maximálně 80 TB.
> - Historie souborů a snímky objektů BLOB se neexportují.


