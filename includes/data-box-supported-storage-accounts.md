---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242154"
---
Tady je seznam účtů úložiště podporuje a typy úložiště pro zařízení Data Box. Úplný seznam všech různých typů účtů úložiště a jejich úplné možnosti najdete v tématu [typy účtů úložiště](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Účet úložiště / podporované typy úložiště** | **Objekt blob bloku** |**Objekt blob stránky*** |**Služba soubory Azure** |**Poznámky**|
| --- | --- | -- | -- | -- |
| Klasické Standard | Ano | Ano | Ano |
| Standard pro obecné účely v1  | Ano | Ano | Ano | Horká a studená jsou podporovány.|
| Premium pro obecné účely v1  |  | Ano| | |
| Standard pro obecné účely v2  | Ano | Ano | Ano | Horká a studená jsou podporovány.|
| Premium pro obecné účely v2  |  |Ano | | |
| Úložiště objektů BLOB Standard |Ano | | |Horká a studená jsou podporovány. |

\* *-Daty nahranými do objektů BLOB stránky musí být zarovnán jako je například virtuální pevné disky 512 bajtů.*

>[!NOTE]
> Účty Azure Data Lake Storage generace 2 nejsou podporovány.
