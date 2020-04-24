---
title: Analýzy Azure Batch
description: Témata v dávkové analýze obsahují referenční informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.
ms.topic: article
ms.date: 04/20/2017
ms.openlocfilehash: d35bf249a3ae5dd06f42b89c3d5c2773f9508603
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113064"
---
# <a name="batch-analytics"></a>Dávková analýza
Témata v dávkové analýze obsahují referenční informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.

Další informace o povolení a využívání protokolů diagnostiky služby Batch najdete v tématu [protokolování diagnostiky Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) .

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Služba Azure Batch emituje během doby platnosti určitých prostředků služby Batch následující události protokolu diagnostiky.

**Události protokolu služby**
* [Vytvoření fondu](batch-pool-create-event.md)
* [Spuštění odstranění fondu](batch-pool-delete-start-event.md)
* [Odstranění fondu bylo dokončeno.](batch-pool-delete-complete-event.md)
* [Začátek změny velikosti fondu](batch-pool-resize-start-event.md)
* [Změna velikosti fondu dokončena](batch-pool-resize-complete-event.md)
* [Začátek úlohy](batch-task-start-event.md)
* [Úloha dokončena](batch-task-complete-event.md)
* [Selhání úlohy](batch-task-fail-event.md)