---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 54a789aba793d9a6b44d5fe9a1e0f42023002b5a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555507"
---
| **Prostředek** | **Výchozí omezení** | **Maximální omezení** |
| --- | --- | --- |
| Počet účtů Azure Batch na oblast na předplatné | 1–3 |50 |
| Počet vyhrazených jader na účet Batch | 90–900 | Kontaktování podpory |
| Počet jader s nízkou prioritou na účet Batch | 10–100 | Kontaktování podpory |
| **[Aktivní](/rest/api/batchservice/job/get#jobstate)** úlohy a plány úloh na účet Batch (**dokončené** úlohy nejsou omezeny) | 100–300 | 1000<sup>1</sup> |
| Počet fondů na účet Batch | 20–100 | 500<sup>1</sup> |

<sup>1</sup>Pokud chcete požádat o navýšení nad tyto limity, kontaktujte podporu Azure.

> [!IMPORTANT]
> Měníme, jak vyžádáte a spravujete vyhrazenou kvótu.  Celková vyhrazená vCPU je aktuálně vynutilná hodnota, ale brzy využijeme vyhrazenou kvótu pro každou řadu virtuálních počítačů. Kvóta s nízkou prioritou se bude i nadále uplatňovat na základě celkového limitu. nebude vynutila řada virtuálních počítačů.

> [!NOTE]
> Výchozí limity se liší v závislosti na typu předplatného, které použijete k vytvoření účtu Batch. Zobrazené kvóty jader se týkají účtů Batch v režimu služby Batch. [Zobrazení kvót v účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas)

> [!IMPORTANT]
> Abychom vám pomohli lépe spravovat kapacitu během globální zdravotní pandemie, výchozí kvóty počtu jader pro nové účty Batch v některých oblastech a pro některé typy předplatného se snížily z výše uvedených rozsahů hodnot, v některých případech až na nulový počet jader. Když vytváříte nový účet Batch, [ověřte svoji kvótu počtu jader](../articles/batch/batch-quota-limit.md#view-batch-quotas), a pokud je to třeba, [požádejte o zvýšení kvóty počtu jader](../articles/batch/batch-quota-limit.md#increase-a-quota). Případně zvažte možnost znovu použít účty Batch, které již mají dostatečnou kvótu.