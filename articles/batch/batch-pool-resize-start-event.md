---
title: Událost zahájení změny velikosti fondu Azure Batch | Microsoft Docs
description: Odkaz na událost zahájení změny velikosti fondu služby Batch
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
ms.openlocfilehash: 668d686f296763f5ed51dc6361691352d22e0738
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094719"
---
# <a name="pool-resize-start-event"></a>Událost zahájení změny velikosti fondu

 Tato událost je generována při zahájení změny velikosti fondu. Vzhledem k tomu, že velikost fondu je asynchronní událost, můžete očekávat, že se má po dokončení operace změny velikosti vygenerovat událost změny velikosti fondu na dokončeno.

 Následující příklad ukazuje tělo události začátek změny velikosti fondu z 0 na 2 uzly s ruční změnou velikosti.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Prvek|type|Poznámky|
|-------------|----------|-----------|
|poolId|Řetězec|ID fondu|
|nodeDeallocationOption|Řetězec|Určuje, kdy se můžou uzly odebírat z fondu, pokud se zmenší velikost fondu.<br /><br /> Možné hodnoty jsou:<br /><br /> **Queue (fronta** ) – ukončí spuštěné úlohy a znovu je zařadí do fronty. Úkoly se spustí znovu, až bude úloha povolená. Po ukončení úloh odeberte uzly.<br /><br /> **ukončit** – ukončí spuštěné úlohy. Úkoly se znovu nespustí. Po ukončení úloh odeberte uzly.<br /><br /> **taskcompletion** – povolí dokončení aktuálně spuštěných úloh. Naplánujte během čekání žádné nové úlohy. Po dokončení všech úloh odeberte uzly.<br /><br /> **Retaineddata** – umožňuje dokončit aktuálně spuštěné úlohy a pak počkat na vypršení platnosti všech dob uchovávání dat úkolů. Naplánujte během čekání žádné nové úlohy. Odebrat uzly, pokud vypršela platnost všech dob uchovávání úkolů.<br /><br /> Výchozí hodnota je requeue.<br /><br /> Pokud se velikost fondu zvyšuje, hodnota je nastavená na **neplatné**.|
|currentDedicated|Int32|Počet výpočetních uzlů, které jsou aktuálně přiřazeny ke fondu.|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou pro fond požadovány.|
|enableAutoScale|Bool|Určuje, jestli se velikost fondu v průběhu času automaticky upraví.|
|isAutoPool|Bool|Určuje, jestli se fond vytvořil pomocí mechanismu autopoolu úlohy.|
