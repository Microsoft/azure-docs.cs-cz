---
title: Událost dokončení změny velikosti fondu Azure Batch
description: Odkaz na událost dokončení změny velikosti fondu Batch Podívejte se na příklad fondu, který se zvýšil a byl úspěšně dokončen.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 9d3342587b5f6e0e134f4295a8c79deeb23df94b
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803642"
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

|Prvek|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
|`nodeDeallocationOption`|Řetězec|Určuje, kdy se můžou uzly odebírat z fondu, pokud se zmenší velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **Queue (fronta** ) – ukončí spuštěné úlohy a znovu je zařadí do fronty. Úkoly se spustí znovu, až bude úloha povolená. Po ukončení úloh odeberte uzly.<br /><br /> **ukončit** – ukončí spuštěné úlohy. Úkoly se znovu nespustí. Po ukončení úloh odeberte uzly.<br /><br /> **taskcompletion** – povolí dokončení aktuálně spuštěných úloh. Naplánujte během čekání žádné nové úlohy. Po dokončení všech úloh odeberte uzly.<br /><br /> **Retaineddata** – umožňuje dokončit aktuálně spuštěné úlohy a pak počkat na vypršení platnosti všech dob uchovávání dat úkolů. Naplánujte během čekání žádné nové úlohy. Odebrat uzly, pokud vypršela platnost všech dob uchovávání úkolů.<br /><br /> Výchozí hodnota je requeue.<br /><br /> Pokud se velikost fondu zvyšuje, hodnota je nastavená na **neplatné**.|
|`currentDedicatedNodes`|Int32|Počet vyhrazených výpočetních uzlů, které jsou aktuálně přiřazeny ke fondu.|
|`targetDedicatedNodes`|Int32|Počet vyhrazených výpočetních uzlů, které jsou požadovány pro fond.|
|`currentLowPriorityNodes`|Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou aktuálně přiřazeny ke fondu.|
|`targetLowPriorityNodes`|Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou pro fond požadovány.|
|`enableAutoScale`|Logická hodnota|Určuje, jestli se velikost fondu v průběhu času automaticky upraví.|
|`isAutoPool`|Logická hodnota|Určuje, jestli se fond vytvořil pomocí mechanismu autopoolu úlohy.|
|`startTime`|DateTime|Čas zahájení změny velikosti fondu.|
|`endTime`|DateTime|Čas, kdy se změna velikosti fondu dokončila.|
|`resultCode`|Řetězec|Výsledek změny velikosti.|
|`resultMessage`|Řetězec| Podrobná zpráva o výsledku.<br /><br /> Pokud se změna velikosti úspěšně dokončila, uvádí, že operace byla úspěšná.|
