---
title: Analýzy Azure Batch
description: Témata v dávkové analýze obsahují referenční informace o událostech a výstrahách, které jsou k dispozici pro prostředky služby Batch.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91849507"
---
# <a name="batch-analytics"></a>Dávková analýza

Témata v této části obsahují referenční informace o událostech a výstrahách dostupných pro prostředky služby Batch.

Další informace o povolení a využívání protokolů diagnostiky služby Batch najdete v tématu [protokolování diagnostiky Azure Batch](./batch-diagnostics.md) .

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Služba Azure Batch emituje během doby platnosti určitých prostředků služby Batch následující události protokolu diagnostiky.

### <a name="service-log-events"></a>Události protokolu služby

- [Vytvoření fondu](batch-pool-create-event.md)
- [Spuštění odstranění fondu](batch-pool-delete-start-event.md)
- [Odstranění fondu bylo dokončeno.](batch-pool-delete-complete-event.md)
- [Začátek změny velikosti fondu](batch-pool-resize-start-event.md)
- [Změna velikosti fondu dokončena](batch-pool-resize-complete-event.md)
- [Automatické škálování fondu](batch-pool-autoscale-event.md)
- [Začátek úlohy](batch-task-start-event.md)
- [Úloha dokončena](batch-task-complete-event.md)
- [Selhání úlohy](batch-task-fail-event.md)
- [Plán úlohy selže](batch-task-schedule-fail-event.md)
