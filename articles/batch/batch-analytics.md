---
title: Analýzy Azure Batch
description: Témata v dávkové analýze obsahují referenční informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 5f5063ff2b285a0613d8c7bf367007997afcb778
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935256"
---
# <a name="batch-analytics"></a>Dávková analýza
Témata v dávkové analýze obsahují referenční informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.

Další informace o povolení a využívání protokolů diagnostiky služby Batch najdete v tématu [protokolování diagnostiky Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) .

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Služba Azure Batch emituje během doby platnosti určitých prostředků služby Batch následující události protokolu diagnostiky.

**Služba Protokol událostí**
* [Vytvoření fondu](batch-pool-create-event.md)
* [Zahájení odstranění fondu](batch-pool-delete-start-event.md)
* [Dokončení odstranění fondu](batch-pool-delete-complete-event.md)
* [Zahájení změny velikosti fondu](batch-pool-resize-start-event.md)
* [Dokončení změny velikosti fondu](batch-pool-resize-complete-event.md)
* [Zahájení úlohy](batch-task-start-event.md)
* [Úloha je dokončená](batch-task-complete-event.md)
* [Selhání úlohy](batch-task-fail-event.md)