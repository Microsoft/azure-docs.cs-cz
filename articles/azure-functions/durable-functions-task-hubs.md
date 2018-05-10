---
title: Úloha centra v trvanlivý funkce – Azure
description: Zjistěte, jaké úlohu rozbočovači je v rozšíření trvanlivý funkce pro Azure Functions. Naučte se konfigurovat konfigurace úloh rozbočovače.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 563667684accf8b434052cd412bf6e93c77ea63a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Úloha centra trvanlivý funkcí (Azure Functions)

A *úloh centra* v [trvanlivý funkce](durable-functions-overview.md) je logický kontejner pro prostředky úložiště Azure, které se používají pro orchestrations. Funkce produktu Orchestrator a aktivity můžete vzájemné interakce pouze při náleží do stejné úlohy rozbočovače.

Každá funkce aplikace má rozbočovač samostatná úloha. Pokud se více aplikací funkce sdílet účet úložiště, účet úložiště obsahuje více úkolů rozbočovače. Následující diagram znázorňuje jednu úlohu rozbočovače jednotlivé funkce aplikace v účtech úložiště sdílené a vyhrazené.

![Diagram zobrazující sdílený a vyhrazené účty úložiště.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Prostředky Azure Storage

Rozbočovač úloha se skládá z následujících prostředků úložiště: 

* Jednu či více front ovládacího prvku.
* Jedna fronta pracovní položku.
* Tabulka jeden historie.
* Jedna instance tabulka.
* Jeden kontejner úložiště obsahující jeden nebo více objektů BLOB zapůjčení.

Všechny tyto prostředky se vytvoří automaticky v výchozí účet úložiště Azure při orchestrator nebo funkce aktivity spustit nebo mají naplánované spuštění. [Výkonu a možností škálování](durable-functions-perf-and-scale.md) článek vysvětluje, jak se používají tyto prostředky.

## <a name="task-hub-names"></a>Názvy rozbočovačů úloh

Úloha centra jsou identifikovány názvem, který je deklarován v *host.json* souboru, jak je znázorněno v následujícím příkladu:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Úloha rozbočovače názvy musí začínat písmenem a obsahovat jenom písmena a čísla. Pokud není zadáno, výchozí název je **DurableFunctionsHub**.

> [!NOTE]
> Název je jedna úloha rozbočovače co odlišuje od jiného existuje více úkolů centra v účtu sdílené úložiště. Pokud máte víc aplikací funkce sdílení účet sdílené úložiště, budete muset nakonfigurovat odlišné názvy pro každý rozbočovač úloh v *host.json* soubory.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak zpracovat Správa verzí](durable-functions-versioning.md)
