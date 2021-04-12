---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 12/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0a94b122f1cdd598eeac553c8cc784d2a0a5369f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97614505"
---
| **Prostředek** | **Výchozí omezení** | **Maximální omezení** |
| --- | --- | --- |
| Počet účtů Azure Batch na oblast na předplatné | 1–3 |50 |
| Počet vyhrazených jader na účet Batch | 90–900 | Kontaktování podpory |
| Počet jader s nízkou prioritou na účet Batch | 10–100 | Kontaktování podpory |
| **[Aktivní](/rest/api/batchservice/job/get#jobstate)** úlohy a plány úloh na účet Batch (**dokončené** úlohy nejsou omezeny) | 100–300 | 1000<sup>1</sup> |
| Počet fondů na účet Batch | 20–100 | 500<sup>1</sup> |

<sup>1</sup>Pokud chcete požádat o navýšení nad tyto limity, kontaktujte podporu Azure.

> [!NOTE]
> Výchozí limity se liší v závislosti na typu předplatného, které použijete k vytvoření účtu Batch. Zobrazené kvóty jader se týkají účtů Batch v režimu služby Batch. [Zobrazení kvót v účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas)

> [!IMPORTANT]
> Abychom vám pomohli lépe spravovat kapacitu během globální zdravotní pandemie, výchozí kvóty počtu jader pro nové účty Batch v některých oblastech a pro některé typy předplatného se snížily z výše uvedených rozsahů hodnot, v některých případech až na nulový počet jader. Když vytváříte nový účet Batch, [ověřte svoji kvótu počtu jader](../articles/batch/batch-quota-limit.md#view-batch-quotas), a pokud je to třeba, [požádejte o zvýšení kvóty počtu jader](../articles/batch/batch-quota-limit.md#increase-a-quota). Případně zvažte možnost znovu použít účty Batch, které již mají dostatečnou kvótu.