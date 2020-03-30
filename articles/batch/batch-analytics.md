---
title: Azure Batch Analytics
description: Témata v Batch Analytics obsahují referenční informace pro události a výstrahy dostupné pro prostředky dávkové služby.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025958"
---
# <a name="batch-analytics"></a>Dávková analýza
Témata v Batch Analytics obsahují referenční informace pro události a výstrahy dostupné pro prostředky dávkové služby.

Další informace o povolení a využívání protokolů diagnostiky batch najdete v [tématu Protokolování diagnostiky Azure Batch.](https://azure.microsoft.com/documentation/articles/batch-diagnostics/)

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Služba Azure Batch vydává následující události diagnostického protokolu během doby životnosti určitých prostředků Batch.

**Události protokolu služeb**
* [Vytvoření fondu](batch-pool-create-event.md)
* [Začátek odstranění fondu](batch-pool-delete-start-event.md)
* [Odstranění fondu dokončeno](batch-pool-delete-complete-event.md)
* [Začátek změny velikosti fondu](batch-pool-resize-start-event.md)
* [Změna velikosti fondu dokončena](batch-pool-resize-complete-event.md)
* [Zahájení úkolu](batch-task-start-event.md)
* [Úkol byl dokončen.](batch-task-complete-event.md)
* [Úloha se nezdaří](batch-task-fail-event.md)