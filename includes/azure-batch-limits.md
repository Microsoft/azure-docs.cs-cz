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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81734304"
---
| **Prostředek** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Účty Azure Batch na oblast na předplatné | 1-3 |50 |
| Vyhrazená jádra na dávkový účet | 90-900 | Kontaktování podpory |
| Jádra s nízkou prioritou na dávkový účet | 10-100 | Kontaktování podpory |
| **[Aktivní](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** úlohy a plány úloh na dávkový účet **(dokončené** úlohy nemají žádné omezení) | 100-300 | 1 000<sup>1</sup> |
| Počet fondů na účet Batch | 20-100 | 500<sup>1.</sup> |

<sup>1.</sup> Chcete-li požádat o zvýšení nad tento limit, obraťte se na podporu Azure.

> [!NOTE]
> Výchozí limity se liší v závislosti na typu předplatného, které používáte k vytvoření účtu Batch. Zobrazené kvóty jader jsou určeny pro dávkové účty v režimu dávkové služby. [Zobrazení kvót v účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Abychom mohli lépe spravovat kapacitu během globální pandemie stavu, výchozí kvóty jádra pro nové účty Batch v některých regionech a pro některé typy předplatného byly sníženy z výše uvedeného rozsahu hodnot, v některých případech na nulová jádra. Při vytváření nového účtu Batch [zkontrolujte základní kvótu](../articles/batch/batch-quota-limit.md#view-batch-quotas) a v případě potřeby [požádejte o zvýšení kvóty jádra](../articles/batch/batch-quota-limit.md#increase-a-quota). 
