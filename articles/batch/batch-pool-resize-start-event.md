---
title: Událost zahájení změny velikosti fondu Azure Batch
description: Odkaz na událost zahájení změny velikosti fondu dávek Příklad ukazuje tělo fondu změnit velikost počáteční událost pro fond změna velikosti od 0 do 2 uzlů s ruční změny velikosti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023578"
---
# <a name="pool-resize-start-event"></a>Událost zahájení změny velikosti fondu

 Tato událost je vyzařována při spuštění změny velikosti fondu. Vzhledem k tomu, že změna velikosti fondu je asynchronní událost, můžete očekávat, že velikost fondu dokončení události, které mají být emitovány po dokončení operace změny velikosti.

 Následující příklad ukazuje tělo události zahájení změny velikosti fondu pro velikost fondu od 0 do 2 uzlů s ruční množinou.

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
|`id`|Řetězec|ID bazénu.|
|`nodeDeallocationOption`|Řetězec|Určuje, kdy mohou být uzly odebrány z fondu, pokud se zmenšuje velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **opětovné zařazení do fronty** – Ukončit spuštěné úlohy a znovu je zařadit do fronty. Úlohy budou spuštěny znovu, pokud je úloha povolena. Odeberte uzly, jakmile jsou úkoly ukončeny.<br /><br /> **ukončit** – ukončit spuštěné úlohy. Úlohy nebudou znovu spuštěny. Odeberte uzly, jakmile jsou úkoly ukončeny.<br /><br /> **dokončení úkolu** – povolit dokončení aktuálně spuštěných úloh. Během čekání nenaplánujte žádné nové úkoly. Po dokončení všech úkolů odeberte uzly.<br /><br /> **Uchovávaná data** – Povolit dokončení aktuálně spuštěných úloh a počkejte, až vyprší platnost všech dob uchovávání dat úkolů. Během čekání nenaplánujte žádné nové úkoly. Po uplynutí všech období uchování úkolů odeberte uzly.<br /><br /> Výchozí hodnota je přeřadit do fronty.<br /><br /> Pokud se velikost fondu zvětšuje, je hodnota nastavena na **neplatnou**.|
|`currentDedicatedNodes`|Int32|Počet výpočetních uzlů aktuálně přiřazených do fondu.|
|`targetDedicatedNodes`|Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|`currentLowPriorityNodes`|Int32|Počet výpočetních uzlů aktuálně přiřazených do fondu.|
|`targetLowPriorityNodes`|Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|`enableAutoScale`|Logická hodnota|Určuje, zda se velikost fondu v průběhu času automaticky upravuje.|
|`isAutoPool`|Logická hodnota|Určuje, zda byl fond vytvořen pomocí mechanismu automatického spoluvytváření úlohy.|
