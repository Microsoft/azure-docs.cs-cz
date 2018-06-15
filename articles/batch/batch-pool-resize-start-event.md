---
title: Počáteční událost velikosti fondu Azure Batch | Microsoft Docs
description: Referenční dokumentace pro událost počáteční změny velikosti fondu Batch.
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
ms.openlocfilehash: b4412764c313669dc154fccaa56f22417262994d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312605"
---
# <a name="pool-resize-start-event"></a>Událost zahájení změny velikosti fondu

 Tato událost je vygenerované při bylo zahájeno změny velikosti fondu. Vzhledem k tomu, že změny velikosti fondu je asynchronní událostí, můžete očekávat, že událost complete změny velikosti fondu pro vypuštění po dokončení operace změny velikosti.

 Následující příklad ukazuje text počáteční událost změny velikosti fondu pro fondu změnu velikosti od 0 do 2 uzlů se ruční změny velikosti.

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
|nodeDeallocationOption|Řetězec|Určuje, kdy může odebrat uzly ve fondu, pokud zmenšování velikosti fondu.<br /><br /> Možné hodnoty:<br /><br /> **requeue** – ukončí se všechny spuštěné úlohy a znovu je. Úkoly se znovu spustí, když bude povolena úloha. Odeberte uzly, jakmile úkoly ukončí.<br /><br /> **Ukončit** – ukončit spuštěné úkoly. Úkoly se znovu nespustí. Odeberte uzly, jakmile úkoly ukončí.<br /><br /> **taskcompletion** – povolit aktuálně spuštěných úloh k dokončení. Plánovat žádné nové úkoly při čekání. Odeberte uzly po dokončení všech úloh.<br /><br /> **Retaineddata** -povolit aktuálně spuštěných úkolů dokončily. pak počkejte, než všech úkolů období uchovávání dat vyprší. Plánovat žádné nové úkoly při čekání. Odeberte uzly, pokud vypršela platnost období uchovávání všech úloh.<br /><br /> Výchozí hodnota je requeue.<br /><br /> Pokud je zvýšení velikosti fondu, pak je hodnota nastavená **neplatný**.|
|currentDedicated|Int32|Počet aktuálně přiřazená k fondu výpočetních uzlů.|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|enableAutoScale|Logická hodnota (Bool)|Určuje, zda velikost fondu automaticky přizpůsobí v čase.|
|isAutoPool|Logická hodnota (Bool)|Speficies jestli fondu se vytvořil prostřednictvím mechanismu AutoPool úlohy.|
