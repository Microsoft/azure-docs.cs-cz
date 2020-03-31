---
title: Událost selhání úlohy Azure Batch
description: Odkaz na událost selhání dávkové úlohy. Tato událost bude emitován a dokončení úkolu a lze ji použít ke zjištění, kdy se úloha nezdařila.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/15/2019
ms.author: labrenne
ms.openlocfilehash: 2bc958d6dca2b3caae665e6f9b080c651ace9ea0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022881"
---
# <a name="task-fail-event"></a>Událost selhání úlohy

 Tato událost je vyzařována po dokončení úlohy s chybou. V současné době jsou všechny nenulové ukončovací kódy považovány za chyby. Tato událost bude emitován *a dokončení* úkolu a lze ji použít ke zjištění, kdy se úloha nezdařila.


 Následující příklad ukazuje tělo události selhání úlohy.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`jobId`|Řetězec|ID úlohy obsahující úkol.|
|`id`|Řetězec|ID úkolu.|
|`taskType`|Řetězec|Typ úkolu. Může se jedná buď o "JobManager", což znamená, že se jedná o úlohu správce úlohy, nebo o "uživatel", což znamená, že se nejedná o úlohu správce úloh. Tato událost není vydávána pro úkoly přípravy úloh, úkoly uvolnění úlohy nebo zahájení úkolů.|
|`systemTaskVersion`|Int32|Toto je vnitřní čítač opakování úkolu. Interně služba Batch může opakovat úkol tak, aby odpovídal přechodným problémům. Tyto problémy mohou zahrnovat interní plánování chyby nebo pokusy o obnovení z výpočetních uzlů ve špatném stavu.|
|[`nodeInfo`](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetníuzel, na kterém byla úloha spuštěna.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplexní typ|Určuje, že úloha je úloha s více instancemi, která vyžaduje více výpočetních uzlů.  Viz [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) podrobnosti.|
|[`constraints`](#constraints)|Komplexní typ|Omezení spuštění, které se vztahují k tomuto úkolu.|
|[`executionInfo`](#executionInfo)|Komplexní typ|Obsahuje informace o provádění úkolu.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a>nodeInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`poolId`|Řetězec|ID fondu, na kterém byla úloha spuštěna.|
|`nodeId`|Řetězec|ID uzlu, na kterém byla úloha spuštěna.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a>multiInstanceSettings

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Počet výpočetních uzlů vyžadované úlohou.|

###  <a name="constraints"></a><a name="constraints"></a>Omezení

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maximální počet opakování úlohy. Služba Batch opakuje úlohu, pokud je její ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch se pokusí o úkol jednou a pak může opakovat až do tohoto limitu. Například pokud maximální počet opakování je 3, Batch pokusí úkol až 4 krát (jeden počáteční pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch neprovádí opakování úloh.<br /><br /> Pokud je maximální počet opakování -1, služba Batch opakuje úkoly bez omezení.<br /><br /> Výchozí hodnota je 0 (žádné opakování).|


###  <a name="executioninfo"></a><a name="executionInfo"></a>executionInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`startTime`|DateTime|Čas spuštění úlohy. 'Spuštěno' odpovídá **spuštěnému** stavu, takže pokud úloha určuje soubory prostředků nebo balíčky aplikací, pak čas zahájení odráží čas, kdy úloha začala stahovat nebo nasazovat tyto.  Pokud byla úloha restartována nebo opakována, jedná se o poslední čas, kdy byla úloha spuštěna.|
|`endTime`|DateTime|Čas dokončení úkolu.|
|`exitCode`|Int32|Ukončovací kód úlohy.|
|`retryCount`|Int32|Počet opakování úlohy službou Batch. Úloha je opakována, pokud je ukončena s nenulovým ukončovacím kódem, až po zadaný MaxTaskRetryCount.|
|`requeueCount`|Int32|Počet, kolikrát byla úloha znovu zařazena do fronty službou Batch jako výsledek požadavku uživatele.<br /><br /> Když uživatel odebere uzly z fondu (změnami velikosti nebo zmenšením fondu) nebo když je úloha zakázána, může uživatel určit, že spuštěné úlohy na uzlech budou znovu zařazeny do fronty pro spuštění. Tento počet sleduje, kolikrát byla úloha z těchto důvodů znovu zařazena do fronty.|
