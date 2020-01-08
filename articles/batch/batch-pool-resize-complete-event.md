---
title: Událost dokončení změny velikosti fondu Azure Batch
description: Odkaz na událost dokončení změny velikosti fondu Batch Podívejte se na příklad fondu, který se zvýšil a byl úspěšně dokončen.
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
ms.openlocfilehash: 44d7aaa60ee89a1ec6b0b98c38b83038fc4e70d4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449775"
---
# <a name="pool-resize-complete-event"></a>Událost dokončení změny velikosti fondu

 Tato událost se vygeneruje, když se změna velikosti fondu dokončí nebo se nezdařila.

 Následující příklad zobrazuje tělo události změny velikosti fondu, která je dokončena pro fond, který se zvětšil a byl úspěšně dokončen.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
|`nodeDeallocationOption`|Řetězec|Určuje, kdy se můžou uzly odebírat z fondu, pokud se zmenší velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **Queue (fronta** ) – ukončí spuštěné úlohy a znovu je zařadí do fronty. Úkoly se spustí znovu, až bude úloha povolená. Po ukončení úloh odeberte uzly.<br /><br /> **ukončit** – ukončí spuštěné úlohy. Úkoly se znovu nespustí. Po ukončení úloh odeberte uzly.<br /><br /> **taskcompletion** – povolí dokončení aktuálně spuštěných úloh. Naplánujte během čekání žádné nové úlohy. Po dokončení všech úloh odeberte uzly.<br /><br /> **Retaineddata** – umožňuje dokončit aktuálně spuštěné úlohy a pak počkat na vypršení platnosti všech dob uchovávání dat úkolů. Naplánujte během čekání žádné nové úlohy. Odebrat uzly, pokud vypršela platnost všech dob uchovávání úkolů.<br /><br /> Výchozí hodnota je requeue.<br /><br /> Pokud se velikost fondu zvyšuje, hodnota je nastavená na **neplatné**.|
|`currentDedicatedNodes`|Datový typ Int32|Počet vyhrazených výpočetních uzlů, které jsou aktuálně přiřazeny ke fondu.|
|`targetDedicatedNodes`|Datový typ Int32|Počet vyhrazených výpočetních uzlů, které jsou požadovány pro fond.|
|`currentLowPriorityNodes`|Datový typ Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou aktuálně přiřazeny ke fondu.|
|`targetLowPriorityNodes`|Datový typ Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou pro fond požadovány.|
|`enableAutoScale`|Bool|Určuje, jestli se velikost fondu v průběhu času automaticky upraví.|
|`isAutoPool`|Bool|Určuje, jestli se fond vytvořil pomocí mechanismu autopoolu úlohy.|
|`startTime`|Datum a čas|Čas zahájení změny velikosti fondu.|
|`endTime`|Datum a čas|Čas, kdy se změna velikosti fondu dokončila.|
|`resultCode`|Řetězec|Výsledek změny velikosti.|
|`resultMessage`|Řetězec| Podrobná zpráva o výsledku.<br /><br /> Pokud se změna velikosti úspěšně dokončila, uvádí, že operace byla úspěšná.|
