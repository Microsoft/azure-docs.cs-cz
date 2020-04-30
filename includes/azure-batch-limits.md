---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81734304"
---
| **Prostředek** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Účty Azure Batch na oblast na základě předplatného | 1-3 |50 |
| Vyhrazená jádra na účet Batch | 90-900 | Kontaktování podpory |
| Jádra s nízkou prioritou na účet Batch | 10-100 | Kontaktování podpory |
| **[Aktivní](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** úlohy a plány úloh na účet Batch (**dokončené** úlohy nemají žádné omezení) | 100-300 | 1 000<sup>1</sup> |
| Počet fondů na účet Batch | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Pokud chcete požádat o zvýšení nad rámec tohoto limitu, obraťte se na podporu Azure.

> [!NOTE]
> Výchozí omezení se liší v závislosti na typu předplatného, které použijete k vytvoření účtu Batch. Zobrazené kvóty jader jsou pro účty Batch v režimu služby Batch. [Zobrazení kvót v účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Abychom vám pomohli lépe spravovat kapacitu během globální služby Health PANDEMIC, výchozí základní kvóty pro nové účty Batch v některých oblastech a pro některé typy předplatného se snížily z výše uvedených hodnot, v některých případech na nulový počet jader. Když vytváříte nový účet Batch, [zkontrolujete základní kvótu](../articles/batch/batch-quota-limit.md#view-batch-quotas) a [požádejte o zvýšení kvóty jádra](../articles/batch/batch-quota-limit.md#increase-a-quota), pokud je to potřeba. 
