---
title: Zahájit událost Azure změny velikosti fondu služby Batch | Dokumentace Microsoftu
description: Referenční informace pro událost zahájení změny velikosti fondu služby Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 63abeaca5a9c87945671e79a9c8d7a2512d0d777
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471330"
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

|Element|Type|Poznámky|
|-------------|----------|-----------|
|poolId|String|Id fondu.|
|nodeDeallocationOption|String|Určuje, když uzlů může být odebrán z fondu, pokud se snižuje velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **znovuzařazení do fronty** – ukončí běžící úlohy a zařadí. Úkoly se znovu spustí, když je úloha povolená. Odebrání uzlů co nejdříve po ukončení úkolů.<br /><br /> **Ukončit** – spuštěné úkoly ukončit. Úkoly se nespustí. Odebrání uzlů co nejdříve po ukončení úkolů.<br /><br /> **taskcompletion** – povolit aktuálně spuštěné úkoly k dokončení. Plánovat žádné nové úkoly při čekání. Odebrání uzlů po dokončení všech úloh.<br /><br /> **Retaineddata** -povolit aktuálně spuštěným úkolům se dokončily. pak všech úkolů období uchovávání dat se vypršení platnosti. Plánovat žádné nové úkoly při čekání. Odebrání uzlů po období uchovávání všech úkolů vypršela.<br /><br /> Výchozí hodnota je znovuzařazení do fronty.<br /><br /> Pokud je zvýšení velikosti fondu, pak je hodnota nastavena na **neplatný**.|
|currentDedicated|Int32|Počet aktuálně přiřazená k fondu výpočetních uzlů.|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|enableAutoScale|Bool|Určuje, zda se velikost fondu automaticky přizpůsobí v čase.|
|isAutoPool|Bool|Určuje, zda že byl fond vytvořen prostřednictvím mechanismu AutoPool úlohy.|
