---
title: Azure Batch Analytics | Microsoft Docs
description: Referenční informace pro Azure Batch Analytics
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 03cb6231a42e27c474e20f4c6ae91095156bb766
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095519"
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