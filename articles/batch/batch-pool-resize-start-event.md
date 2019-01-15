---
title: Zahájit událost Azure změny velikosti fondu služby Batch | Dokumentace Microsoftu
description: Referenční informace pro událost zahájení změny velikosti fondu služby Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 3cabf18cfd771151e62d64dc1d2b47b250ac5471
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258267"
---
# <a name="pool-resize-start-event"></a>Událost zahájení změny velikosti fondu

 Tato událost je vygenerován po spuštění změny velikosti fondu. Protože asynchronní událost změny velikosti fondu, můžete očekávat událost dokončení změny velikosti fondu emitování po dokončení operace změny velikosti.

 Následující příklad ukazuje textu událost zahájení změny velikosti fondu pro fondu změnu velikosti od 0 do 2 uzly s ruční změny velikosti.

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

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|poolId|Řetězec|Id fondu.|
|nodeDeallocationOption|Řetězec|Určuje, když uzlů může být odebrán z fondu, pokud se snižuje velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **znovuzařazení do fronty** – ukončí běžící úlohy a zařadí. Úkoly se znovu spustí, když je úloha povolená. Odebrání uzlů co nejdříve po ukončení úkolů.<br /><br /> **Ukončit** – spuštěné úkoly ukončit. Úkoly se nespustí. Odebrání uzlů co nejdříve po ukončení úkolů.<br /><br /> **taskcompletion** – povolit aktuálně spuštěné úkoly k dokončení. Plánovat žádné nové úkoly při čekání. Odebrání uzlů po dokončení všech úloh.<br /><br /> **Retaineddata** -povolit aktuálně spuštěným úkolům se dokončily. pak všech úkolů období uchovávání dat se vypršení platnosti. Plánovat žádné nové úkoly při čekání. Odebrání uzlů po období uchovávání všech úkolů vypršela.<br /><br /> Výchozí hodnota je znovuzařazení do fronty.<br /><br /> Pokud je zvýšení velikosti fondu, pak je hodnota nastavena na **neplatný**.|
|currentDedicated|Datový typ Int32|Počet aktuálně přiřazená k fondu výpočetních uzlů.|
|targetDedicated|Datový typ Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|enableAutoScale|BOOL|Určuje, zda se velikost fondu automaticky přizpůsobí v čase.|
|isAutoPool|BOOL|Určuje, zda že byl fond vytvořen prostřednictvím mechanismu AutoPool úlohy.|
