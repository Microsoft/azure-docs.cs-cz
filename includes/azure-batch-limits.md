---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c98a2146a019817152be9fae76638dbaa4d9de3d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49458832"
---
| **Prostředek** | **Výchozí omezení** | **Maximální omezení** |
| --- | --- | --- |
| Počet účtů Batch na oblast na předplatné | 1 - 3 |50 |
| Počet vyhrazených jader na účet Batch | 10 – 100 | Neuvedeno<sup>1</sup> |
| Počet jader s nízkou prioritou na účet Batch | 10 – 100 | NENÍ K DISPOZICI<sup>2</sup> |
| Aktivní úlohy a plány úloh<sup>3</sup> jeden účet Batch | 100 - 300 | 1000<sup>4</sup> |
| Počet fondů na účet Batch | 20 - 100 | 500<sup>4</sup> |

> [!NOTE]
> Výchozí omezení se liší v závislosti na typu vašeho předplatného, které používáte k vytvoření účtu Batch. Kvóty jader, zobrazí se pro účty Batch v režimu služby Batch. [Zobrazit kvóty účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup> počet vyhrazených jader na účet Batch je možné zvýšit, ale maximální počet není uvedený. Požádejte podporu Azure o zvýšení možnosti.

<sup>2</sup> počtu jader s nízkou prioritou na účet Batch je možné zvýšit, ale maximální počet není uvedený. Požádejte podporu Azure o zvýšení možnosti.

<sup>3</sup> dokončené úlohy a plány úloh nejsou omezené.

<sup>4</sup> podpory Azure kontaktu, pokud chcete požádat o zvýšení nad tento limit.
