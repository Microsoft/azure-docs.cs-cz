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
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553347"
---
| **Prostředek** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Účty služby Azure Batch na oblast a předplatné | 1-3 |50 |
| Počet vyhrazených jader na účet Batch | 10-100 | Neuvedeno<sup>1</sup> |
| Počet jader s nízkou prioritou na účet Batch | 10-100 | NENÍ K DISPOZICI<sup>2</sup> |
| Aktivní úlohy a plány úloh<sup>3</sup> jeden účet Batch | 100-300 | 1,000<sup>4</sup> |
| Počet fondů na účet Batch | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Výchozí omezení se liší v závislosti na typu vašeho předplatného, které používáte k vytvoření účtu Batch. Kvóty jader, zobrazí se pro účty Batch v režimu služby Batch. [Zobrazit kvóty účtu Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>počet vyhrazených jader na účet Batch je možné zvýšit, ale maximální počet není uvedený. Který zvýšení, obraťte se na podporu Azure.

<sup>2</sup>počtu jader s nízkou prioritou na účet Batch je možné zvýšit, ale maximální počet není uvedený. Který zvýšení, obraťte se na podporu Azure.

<sup>3</sup>dokončené úlohy a plány úloh nejsou omezené.

<sup>4</sup>nad tento limit zvýšit, kontaktujte podporu Azure.
