---
title: Analýza služby Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro analýzu Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460178"
---
# <a name="batch-analytics"></a>Dávková analýza
Témata v dávkové analýzy obsahují informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.

Zobrazit [protokolování diagnostiky služby Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) pro další informace o povolení a používání diagnostické protokoly služby Batch.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Služba Azure Batch vysílá následující diagnostický protokol události po celou dobu životnosti určitých prostředků služby Batch.

**Služba Protokol událostí**
* [Vytvoření fondu](batch-pool-create-event.md)
* [Zahájení odstranění fondu](batch-pool-delete-start-event.md)
* [Dokončení odstranění fondu](batch-pool-delete-complete-event.md)
* [Zahájení změny velikosti fondu](batch-pool-resize-start-event.md)
* [Dokončení změny velikosti fondu](batch-pool-resize-complete-event.md)
* [Zahájení úlohy](batch-task-start-event.md)
* [Úloha je dokončená](batch-task-complete-event.md)
* [Selhání úlohy](batch-task-fail-event.md)