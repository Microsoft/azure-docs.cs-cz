---
title: Centra úloh v Durable Functions – Azure
description: Zjistěte, jaké úlohy centrum je v rozšíření Durable Functions pro službu Azure Functions. Další informace o konfiguraci Konfigurace centra úloh.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 53c70d4407222a80a9bc948db51294cd3e4e1bb4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092831"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Centra úloh v Durable Functions (Azure Functions)

A *centra úloh* v [Durable Functions](durable-functions-overview.md) je logický kontejner prostředků služby Azure Storage, které se používají pro Orchestrace. Funkce nástroje Orchestrator a aktivity pouze komunikovat mezi sebou když patří do stejné centra úloh.

Každá aplikace function app má Centrum samostatné úlohy. Pokud se více aplikací funkcí sdílet účet úložiště, účet úložiště obsahuje více centra úloh. Následující diagram znázorňuje jedno Centrum úkolů na aplikace function app v účtech úložiště sdílené a vyhrazené.

![Diagram znázorňující sdílené a vyhrazené účty úložiště.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Prostředky Azure Storage

Centra úloh se skládá z následujících prostředků služby storage: 

* Jednu či více front ovládacího prvku.
* Jedna fronta pracovní položky.
* Tabulky historie jeden.
* Jedna instance tabulka.
* Jeden kontejner úložiště obsahuje minimálně jeden objekt BLOB zapůjčení.

Všechny tyto prostředky se vytvoří automaticky v účtu Azure Storage výchozí při orchestrator nebo funkce aktivity spustit nebo jsou naplánovány ke spuštění. [Výkon a škálování](durable-functions-perf-and-scale.md) článek vysvětluje, jak se používají tyto prostředky.

## <a name="task-hub-names"></a>Názvy centra úloh

Centra úloh je identifikována názvem, který je deklarován v *host.json* souboru, jak je znázorněno v následujícím příkladu:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Centrum názvy úloh musí začínat písmenem a obsahovat jenom písmena a číslice. Pokud není zadán, výchozí název je **DurableFunctionsHub**.

> [!NOTE]
> Název je jedno Centrum úkolů co odlišuje od jiného existuje více centra úloh v účtu úložiště sdílené. Pokud máte více aplikací funkcí sdílení účet sdílené úložiště, budete muset nakonfigurovat jiné názvy jednotlivých úloh centra *host.json* soubory.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak zpracovat správy verzí](durable-functions-versioning.md)
