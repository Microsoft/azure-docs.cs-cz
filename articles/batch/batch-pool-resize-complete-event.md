---
title: Změna velikosti dokončené události fondu Azure Batch
description: Odkaz na událost dokončení dokončení velikosti fondu dávek Podívejte se na příklad fondu, který se zvětšil a úspěšně dokončil.
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
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022218"
---
# <a name="pool-resize-complete-event"></a>Událost dokončení změny velikosti fondu

 Tato událost je vyzařována, pokud byla změna velikosti fondu dokončena nebo se nezdařila.

 Následující příklad ukazuje tělo fondu změnit velikost dokončení události pro fond, který zvětšil velikost a úspěšně dokončena.

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
|`id`|Řetězec|ID bazénu.|
|`nodeDeallocationOption`|Řetězec|Určuje, kdy mohou být uzly odebrány z fondu, pokud se zmenšuje velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **opětovné zařazení do fronty** – Ukončit spuštěné úlohy a znovu je zařadit do fronty. Úlohy budou spuštěny znovu, pokud je úloha povolena. Odeberte uzly, jakmile jsou úkoly ukončeny.<br /><br /> **ukončit** – ukončit spuštěné úlohy. Úlohy nebudou znovu spuštěny. Odeberte uzly, jakmile jsou úkoly ukončeny.<br /><br /> **dokončení úkolu** – povolit dokončení aktuálně spuštěných úloh. Během čekání nenaplánujte žádné nové úkoly. Po dokončení všech úkolů odeberte uzly.<br /><br /> **Uchovávaná data** – Povolit dokončení aktuálně spuštěných úloh a počkejte, až vyprší platnost všech dob uchovávání dat úkolů. Během čekání nenaplánujte žádné nové úkoly. Po uplynutí všech období uchování úkolů odeberte uzly.<br /><br /> Výchozí hodnota je přeřadit do fronty.<br /><br /> Pokud se velikost fondu zvětšuje, je hodnota nastavena na **neplatnou**.|
|`currentDedicatedNodes`|Int32|Počet vyhrazených výpočetních uzlů aktuálně přiřazených do fondu.|
|`targetDedicatedNodes`|Int32|Počet vyhrazených výpočetních uzlů, které jsou požadovány pro fond.|
|`currentLowPriorityNodes`|Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou aktuálně přiřazeny do fondu.|
|`targetLowPriorityNodes`|Int32|Počet výpočetních uzlů s nízkou prioritou, které jsou požadovány pro fond.|
|`enableAutoScale`|Logická hodnota|Určuje, zda se velikost fondu v průběhu času automaticky upravuje.|
|`isAutoPool`|Logická hodnota|Určuje, zda byl fond vytvořen pomocí mechanismu automatického spoluvytváření úlohy.|
|`startTime`|DateTime|Čas, kdy se začala změnit velikost fondu.|
|`endTime`|DateTime|Čas dokončení velikosti fondu.|
|`resultCode`|Řetězec|Výsledek změny velikosti.|
|`resultMessage`|Řetězec| Podrobná zpráva o výsledku.<br /><br /> Pokud byla změna velikosti úspěšně dokončena, uvádí, že operace byla úspěšná.|
