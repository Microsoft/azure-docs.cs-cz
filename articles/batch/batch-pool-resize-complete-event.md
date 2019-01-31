---
title: Událost dokončení velikost fondu Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro fond služby Batch změnit velikost událost complete.
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
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474305"
---
# <a name="pool-resize-complete-event"></a>Událost dokončení změny velikosti fondu

 Tato událost je vygenerován, když změny velikosti fondu je dokončená nebo neúspěšná.

 Následující příklad ukazuje tělo událost dokončení změny velikosti fondu pro skupinu, která zvýšil velikosti a byla úspěšně dokončena.

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

|Element|Type|Poznámky|
|-------------|----------|-----------|
|id|String|Id fondu.|
|nodeDeallocationOption|String|Určuje, když uzlů může být odebrán z fondu, pokud se snižuje velikost fondu.<br /><br /> Možné hodnoty:<br /><br /> **znovuzařazení do fronty** – ukončí běžící úlohy a zařadí. Úkoly se znovu spustí, když je úloha povolená. Odebrání uzlů co nejdříve po ukončení úkolů.<br /><br /> **Ukončit** – spuštěné úkoly ukončit. Úkoly se nespustí. Odebrání uzlů co nejdříve po ukončení úkolů.<br /><br /> **taskcompletion** – povolit aktuálně spuštěné úkoly k dokončení. Plánovat žádné nové úkoly při čekání. Odebrání uzlů po dokončení všech úloh.<br /><br /> **Retaineddata** -povolit aktuálně spuštěným úkolům se dokončily. pak všech úkolů období uchovávání dat se vypršení platnosti. Plánovat žádné nové úkoly při čekání. Odebrání uzlů po období uchovávání všech úkolů vypršela.<br /><br /> Výchozí hodnota je znovuzařazení do fronty.<br /><br /> Pokud je zvýšení velikosti fondu, pak je hodnota nastavena na **neplatný**.|
|currentDedicated|Int32|Počet aktuálně přiřazená k fondu výpočetních uzlů.|
|targetDedicated|Int32|Počet výpočetních uzlů, které jsou požadovány pro fond.|
|enableAutoScale|Bool|Určuje, zda se velikost fondu automaticky přizpůsobí v čase.|
|isAutoPool|Bool|Určuje, zda že byl fond vytvořen prostřednictvím mechanismu AutoPool úlohy.|
|startTime|DateTime|Změna velikosti fondu čas spuštění.|
|endTime|DateTime|Čas, kdy změna velikosti fondu dokončit.|
|Kód výsledku|String|Výsledek změnu velikosti.|
|resultMessage|String|Chybě změny velikosti obsahuje podrobnosti o výsledku.<br /><br /> Pokud změny velikosti byla úspěšně dokončena ho stavy, které operace byla úspěšná.|