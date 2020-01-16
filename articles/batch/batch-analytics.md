---
title: Azure Batch Analytics | Microsoft Docs
description: Referenční informace pro Azure Batch Analytics
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
ms.openlocfilehash: 39a8bfb6a48bf55ae9f2ec36f7716959e6ada9dd
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027398"
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