---
title: Událost dokončení změny velikosti fondu Azure Batch | Microsoft Docs
description: Odkaz na událost dokončení změny velikosti fondu Batch
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
ms.openlocfilehash: a11dec8998a77153cd10b6caf72f5885c69b70c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094772"
---
# <a name="pool-resize-complete-event"></a>Událost dokončení změny velikosti fondu

 Tato událost se vygeneruje, když se změna velikosti fondu dokončí nebo se nezdařila.

 Následující příklad zobrazuje tělo události změny velikosti fondu, která je dokončena pro fond, který se zvětšil a byl úspěšně dokončen.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Prvek|type|Poznámky|
|-------------|----------|-----------|
|id|Řetězec|ID fondu|
|nodeDeallocationOption|Řetězec|Určuje, kdy se můžou uzly odebírat z fondu, pokud se zmenší velikost fondu.<br /><br /> Možné hodnoty jsou:<br /><br /> **Queue (fronta** ) – ukončí spuštěné úlohy a znovu je zařadí do fronty. Úkoly se spustí znovu, až bude úloha povolená. Po ukončení úloh odeberte uzly.<br /><br /> **ukončit** – ukončí spuštěné úlohy. Úkoly se znovu nespustí. Po ukončení úloh odeberte uzly.<br /><br /> **taskcompletion** – povolí dokončení aktuálně spuštěných úloh. Naplánujte během čekání žádné nové úlohy. Po dokončení všech úloh odeberte uzly.<br /><br /> **Retaineddata** – umožňuje dokončit aktuálně spuštěné úlohy a pak počkat na vypršení platnosti všech dob uchovávání dat úkolů. Naplánujte během čekání žádné nové úlohy. Odebrat uzly, pokud vypršela platnost všech dob uchovávání úkolů.<br /><br /> Výchozí hodnota je requeue.<br /><br /> Pokud se velikost fondu zvyšuje, hodnota je nastavená na **neplatné**.|
|currentDedicated|Int32|Počet výpočetních uzlů, které jsou aktuálně přiřazeny ke fondu.|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou pro fond požadovány.|
|enableAutoScale|Bool|Určuje, jestli se velikost fondu v průběhu času automaticky upraví.|
|isAutoPool|Bool|Určuje, jestli se fond vytvořil pomocí mechanismu autopoolu úlohy.|
|startTime|DateTime|Čas zahájení změny velikosti fondu.|
|endTime|DateTime|Čas, kdy se změna velikosti fondu dokončila.|
|resultCode|Řetězec|Výsledek změny velikosti.|
|resultMessage|Řetězec|Chyba změny velikosti zahrnuje podrobnosti o výsledku.<br /><br /> Pokud se změna velikosti úspěšně dokončila, uvádí, že operace byla úspěšná.|