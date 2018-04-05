---
title: Událost complete resize fondu Azure Batch | Microsoft Docs
description: Referenční dokumentace pro fondu Batch změnit velikost událost complete.
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
ms.openlocfilehash: e91ba664a69d28cae1f82710d427bd2a391305a2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="pool-resize-complete-event"></a>Událost dokončení změny velikosti fondu

 Tato událost je vygenerované při změny velikosti fondu má byla dokončena nebo se nezdařilo.

 Následující příklad ukazuje text událost complete změny velikosti fondu pro skupinu, která tento nárůst velikosti a byla úspěšně dokončena.

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

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|id|Řetězec|Id fondu.|
|nodeDeallocationOption|Řetězec|Určuje, kdy může odebrat uzly ve fondu, pokud zmenšování velikosti fondu.<br /><br /> Možné hodnoty:<br /><br /> **requeue** – ukončí se všechny spuštěné úlohy a znovu je. Úkoly se znovu spustí, když bude povolena úloha. Odeberte uzly, jakmile úkoly ukončí.<br /><br /> **Ukončit** – ukončit spuštěné úkoly. Úkoly se znovu nespustí. Odeberte uzly, jakmile úkoly ukončí.<br /><br /> **taskcompletion** – povolit aktuálně spuštěných úloh k dokončení. Plánovat žádné nové úkoly při čekání. Odeberte uzly po dokončení všech úloh.<br /><br /> **Retaineddata** -povolit aktuálně spuštěných úkolů dokončily. pak počkejte, než všech úkolů období uchovávání dat vyprší. Plánovat žádné nové úkoly při čekání. Odeberte uzly, pokud vypršela platnost období uchovávání všech úloh.<br /><br /> Výchozí hodnota je requeue.<br /><br /> Pokud je zvýšení velikosti fondu, pak je hodnota nastavená **neplatný**.|
|currentDedicated|Int32|Počet aktuálně přiřazená k fondu výpočetních uzlů.|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|enableAutoScale|Logická hodnota (Bool)|Určuje, zda velikost fondu automaticky přizpůsobí v čase.|
|isAutoPool|Logická hodnota (Bool)|Určuje, zda fondu se vytvořil prostřednictvím mechanismu AutoPool úlohy.|
|startTime|DateTime|Změnit velikost fondu čas spuštění.|
|endTime|DateTime|Čas změnit velikost fondu byla dokončena.|
|resultCode|Řetězec|Výsledek změna.|
|resultMessage|Řetězec|Chyba změny velikosti obsahuje podrobnosti výsledku.<br /><br /> Pokud se změna úspěšně dokončit ho stavy, které operace byla úspěšná.|