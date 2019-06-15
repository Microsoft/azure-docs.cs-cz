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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080900"
---
| **Prostředek** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Účty služby Azure Batch na oblast a předplatné | 1-3 |50 |
| Počet vyhrazených jader na účet Batch | 90-900 | Kontaktujte podporu |
| Počet jader s nízkou prioritou na účet Batch | 10-100 | Kontaktujte podporu |
| **[Aktivní](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)**  úlohy a plány úloh na účet Batch (**Dokončit** úlohy nemají žádné omezení) | 100-300 | 1,000<sup>1</sup> |
| Počet fondů na účet Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Výchozí omezení se liší v závislosti na typu vašeho předplatného, které používáte k vytvoření účtu Batch. Kvóty jader, zobrazí se pro účty Batch v režimu služby Batch. [Zobrazit kvóty účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup>nad tento limit zvýšit, kontaktujte podporu Azure.
