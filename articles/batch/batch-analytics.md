---
title: Analýzy Azure Batch
description: Témata v dávkové analýze obsahují referenční informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 9d0e5c90c7bade82d76c7dbdfd4276e9671c961c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958102"
---
# <a name="batch-analytics"></a>Dávková analýza
Témata v dávkové analýze obsahují referenční informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.

Další informace o povolení a využívání protokolů diagnostiky služby Batch najdete v tématu [protokolování diagnostiky Azure Batch](./batch-diagnostics.md) .

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
