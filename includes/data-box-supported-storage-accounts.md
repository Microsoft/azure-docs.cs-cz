---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "66242154"
---
Tady je seznam podporovaných účtů úložiště a typů úložiště pro Data Box zařízení. Úplný seznam všech různých typů účtů úložiště a jejich úplných možností najdete v tématu [typy účtů úložiště](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Účet úložiště/podporované typy úložišť** | **Objekt blob bloku** |**Objekt blob stránky*** |**Soubory Azure** |**Poznámky**|
| --- | --- | -- | -- | -- |
| Klasický Standard | Ano | Ano | Ano |
| Obecné účely v1 Standard  | Ano | Ano | Ano | Podporují horkou a studenou.|
| Obecné účely v1 Premium  |  | Ano| | |
| Obecné pro účely v2 Standard  | Ano | Ano | Ano | Podporují horkou a studenou.|
| Pro obecné účely v2 Premium  |  |Ano | | |
| Úložiště objektů BLOB Standard |Ano | | |Podporují horkou a studenou. |

\**-Data nahraná do objektů blob stránky musí mít 512 bajtů (například VHD).*

>[!NOTE]
> Účty Azure Data Lake Storage Gen2 se nepodporují.
