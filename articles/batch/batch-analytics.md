---
title: Analýza Azure Batch | Microsoft Docs
description: Referenční informace pro analýzu Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 4c81acb282b24bbd899227c4dcc449fed4ba6c7d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312673"
---
# <a name="batch-analytics"></a>Dávková analýza
Témata v Batch Analytics obsahovat informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.

V tématu [protokolování diagnostiky Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) Další informace o povolení a použití Batch diagnostické protokoly.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Službu Azure Batch vysílá následující události protokolů diagnostiky po dobu životnosti určitých prostředků Batch.

**Služba Protokol událostí**
* [Vytvoření fondu](batch-pool-create-event.md)
* [Spuštění odstranění fondu](batch-pool-delete-start-event.md)
* [Odstranění fondu dokončení](batch-pool-delete-complete-event.md)
* [Počáteční velikosti fondu](batch-pool-resize-start-event.md)
* [Dokončení změny velikosti fondu](batch-pool-resize-complete-event.md)
* [Spuštění úlohy](batch-task-start-event.md)
* [Dokončení úkolů](batch-task-complete-event.md)
* [Selhání úlohy](batch-task-fail-event.md)