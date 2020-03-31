---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080900"
---
| **Zdrojů** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Účty Azure Batch na oblast na předplatné | 1-3 |50 |
| Vyhrazená jádra na dávkový účet | 90-900 | Kontaktování podpory |
| Jádra s nízkou prioritou na dávkový účet | 10-100 | Kontaktování podpory |
| **[Aktivní](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** úlohy a plány úloh na dávkový účet **(dokončené** úlohy nemají žádné omezení) | 100-300 | 1 000<sup>1</sup> |
| Počet fondů na účet Batch | 20-100 | 500<sup>1.</sup> |

> [!NOTE]
> Výchozí limity se liší v závislosti na typu předplatného, které používáte k vytvoření účtu Batch. Zobrazené kvóty jader jsou určeny pro dávkové účty v režimu dávkové služby. [Zobrazení kvót v účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1.</sup> Chcete-li požádat o zvýšení nad tento limit, obraťte se na podporu Azure.
