---
title: Událost zahájení změny velikosti fondu Azure Batch
description: Odkaz na událost zahájení změny velikosti fondu služby Batch Příklad ukazuje tělo události začátek změny velikosti fondu z 0 na 2 uzly s ruční změnou velikosti.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 1866e51da30fe5ed148d019c8720755e99757df7
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023578"
---
# <a name="pool-resize-start-event"></a>Událost zahájení změny velikosti fondu

 Tato událost je generována při zahájení změny velikosti fondu. Vzhledem k tomu, že velikost fondu je asynchronní událost, můžete očekávat, že se má po dokončení operace změny velikosti vygenerovat událost změny velikosti fondu na dokončeno.

 Následující příklad ukazuje tělo události začátek změny velikosti fondu z 0 na 2 uzly s ruční změnou velikosti.

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
|`nodeDeallocationOption`|Řetězec|Určuje, kdy se můžou uzly odebírat z fondu, pokud se zmenší velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **Queue (fronta** ) – ukončí spuštěné úlohy a znovu je zařadí do fronty. Úkoly se spustí znovu, až bude úloha povolená. Po ukončení úloh odeberte uzly.<br /><br /> **ukončit** – ukončí spuštěné úlohy. Úkoly se znovu nespustí. Po ukončení úloh odeberte uzly.<br /><br /> **taskcompletion** – povolí dokončení aktuálně spuštěných úloh. Naplánujte během čekání žádné nové úlohy. Po dokončení všech úloh odeberte uzly.<br /><br /> **Retaineddata** – umožňuje dokončit aktuálně spuštěné úlohy a pak počkat na vypršení platnosti všech dob uchovávání dat úkolů. Naplánujte během čekání žádné nové úlohy. Odebrat uzly, pokud vypršela platnost všech dob uchovávání úkolů.<br /><br /> Výchozí hodnota je requeue.<br /><br /> Pokud se velikost fondu zvyšuje, hodnota je nastavená na **neplatné**.|
|`currentDedicatedNodes`|Datový typ Int32|Počet výpočetních uzlů, které jsou aktuálně přiřazeny ke fondu.|
|`targetDedicatedNodes`|Datový typ Int32|Počet výpočetních uzlů, které jsou pro fond požadovány.|
|`currentLowPriorityNodes`|Datový typ Int32|Počet výpočetních uzlů, které jsou aktuálně přiřazeny ke fondu.|
|`targetLowPriorityNodes`|Datový typ Int32|Počet výpočetních uzlů, které jsou pro fond požadovány.|
|`enableAutoScale`|Bool|Určuje, jestli se velikost fondu v průběhu času automaticky upraví.|
|`isAutoPool`|Bool|Určuje, jestli se fond vytvořil pomocí mechanismu autopoolu úlohy.|
