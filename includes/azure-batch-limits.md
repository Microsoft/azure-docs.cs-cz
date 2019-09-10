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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "67080900"
---
| **Prostředek** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Účty Azure Batch na oblast na základě předplatného | 1-3 |50 |
| Vyhrazená jádra na účet Batch | 90-900 | Kontaktujte podporu |
| Jádra s nízkou prioritou na účet Batch | 10-100 | Kontaktujte podporu |
| **[Aktivní](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** úlohy a plány úloh na účet Batch (**dokončené** úlohy nemají žádné omezení) | 100-300 | 1 000<sup>1</sup> |
| Fondy na účet Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Výchozí omezení se liší v závislosti na typu předplatného, které použijete k vytvoření účtu Batch. Zobrazené kvóty jader jsou pro účty Batch v režimu služby Batch. [Zobrazení kvót v účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Pokud chcete požádat o zvýšení nad rámec tohoto limitu, obraťte se na podporu Azure.
