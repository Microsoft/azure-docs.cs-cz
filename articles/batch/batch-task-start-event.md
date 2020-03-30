---
title: Událost zahájení úlohy Azure Batch
description: Referenční informace pro událost zahájení dávkové úlohy Tato událost je vyzařována, jakmile je naplánováno spuštění úlohy na výpočetním uzlu plánovačem.
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
ms.openlocfilehash: bed3749e29867298f3e8258a08448b7b094055ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022813"
---
# <a name="task-start-event"></a>Událost zahájení úlohy

 Tato událost je vyzařována, jakmile je naplánováno spuštění úlohy na výpočetním uzlu plánovačem. Všimněte si, že pokud je úloha opakována nebo znovu zařazena do fronty, bude tato událost znovu vyzařována pro stejnou úlohu, ale počet opakování a verze systémových úloh budou odpovídajícím způsobem aktualizovány.


 Následující příklad ukazuje text události zahájení úkolu.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
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
        "retryCount": 0
    }
}
```

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`jobId`|Řetězec|ID úlohy obsahující úkol.|
|`id`|Řetězec|ID úkolu.|
|`taskType`|Řetězec|Typ úkolu. Může se jedná buď o "JobManager", což znamená, že se jedná o úlohu správce úlohy, nebo o "uživatel", což znamená, že se nejedná o úlohu správce úloh.|
|`systemTaskVersion`|Int32|Toto je vnitřní čítač opakování úkolu. Interně služba Batch může opakovat úkol tak, aby odpovídal přechodným problémům. Tyto problémy mohou zahrnovat interní plánování chyby nebo pokusy o obnovení z výpočetních uzlů ve špatném stavu.|
|[`nodeInfo`](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetníuzel, na kterém byla úloha spuštěna.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplexní typ|Určuje, že úlohou je úloha více instancí vyžadující více výpočetních uzlů.  Podrobnosti najdete [v tématu multiInstanceSettings.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task)|
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
|`numberOfInstances`|Int|Počet výpočetních uzlů vyžadované úlohou.|

###  <a name="constraints"></a><a name="constraints"></a>Omezení

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maximální počet opakování úlohy. Služba Batch opakuje úlohu, pokud je její ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch se pokusí o úkol jednou a pak může opakovat až do tohoto limitu. Například pokud maximální počet opakování je 3, Batch pokusí úkol až 4 krát (jeden počáteční pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch neprovádí opakování úloh.<br /><br /> Pokud je maximální počet opakování -1, služba Batch opakuje úkoly bez omezení.<br /><br /> Výchozí hodnota je 0 (žádné opakování).|

###  <a name="executioninfo"></a><a name="executionInfo"></a>executionInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`retryCount`|Int32|Počet opakování úlohy službou Batch. Úloha je opakována, pokud je ukončena s nenulovým ukončovacím kódem, až po zadaný MaxTaskRetryCount|
