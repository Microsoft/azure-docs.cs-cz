---
title: Událost selhání úlohy Azure Batch | Microsoft Docs
description: Referenční informace pro událost selhání úlohy Batch
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 68c57fbf510d923c4c87bc180a935965a511dc26
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322903"
---
# <a name="task-fail-event"></a>Událost selhání úlohy

 Tato událost je vyvolána, když je úloha dokončena s chybou. V současné době nejsou všechny nenulové kódy ukončení považovány za selhání. Tato událost bude vygenerována *kromě* události dokončení úkolu a lze ji použít k detekci, kdy se úloha nezdařila.


 Následující příklad ukazuje tělo události selhání úlohy.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
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

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|jobId|Řetězec|ID úlohy obsahující úlohu.|
|id|Řetězec|ID úkolu|
|taskType|Řetězec|Typ úkolu. Může to být buď "JobManager", což značí, že se jedná o úkol správce úloh nebo "uživatel", což značí, že se nejedná o úkol správce úloh. Tato událost není vygenerována pro úlohy přípravy úlohy, úkoly uvolnění úlohy ani pro úlohy zahájení.|
|systemTaskVersion|Int32|Toto je interní čítač opakování na úkolu. Interně může služba Batch Opakovat úlohu, aby se zohlednila přechodná chyba. Tyto problémy mohou zahrnovat interní chyby plánování nebo se pokusí o zotavení z výpočetních uzlů ve špatném stavu.|
|[nodeInfo](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetním uzlu, na kterém byl úkol spuštěn.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplexní typ|Určuje, že úkol je úloha s více instancemi vyžadující více výpočetních uzlů.  Podrobnosti najdete v tématu [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) .|
|[jednotlivým](#constraints)|Komplexní typ|Omezení provádění, která se vztahují na tento úkol.|
|[executionInfo](#executionInfo)|Komplexní typ|Obsahuje informace o provedení úlohy.|

###  <a name="nodeInfo"></a>nodeInfo

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|poolId|Řetězec|ID fondu, ve kterém byl úkol spuštěn.|
|nodeId|Řetězec|ID uzlu, na kterém byl úkol spuštěn.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|numberOfInstances|Int32|Počet výpočetních uzlů vyžadovaných úkolem.|

###  <a name="constraints"></a>jednotlivým

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maximální počet pokusů, kolikrát může být úloha opakována. Služba Batch opakuje úlohu, pokud je její ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch úlohu zopakuje a potom ji můžete opakovat až do tohoto limitu. Například pokud je maximální počet opakování 3, Batch se pokusí úlohu provést až čtyřikrát (jeden počáteční pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch neopakuje úlohy.<br /><br /> Pokud je maximální počet opakování-1, služba Batch zopakuje úlohy bez omezení.<br /><br /> Výchozí hodnota je 0 (žádné opakování).|


###  <a name="executionInfo"></a>executionInfo

|Název elementu|type|Poznámky|
|------------------|----------|-----------|
|startTime|Datetime|Čas spuštění úlohy. ' Running ' odpovídá stavu spuštěno  , takže pokud úloha určí soubory prostředků nebo balíčky aplikací, pak čas spuštění odráží čas, kdy se úloha spustila stahování nebo nasazování.  Pokud byl úkol restartován nebo opakován, jedná se o poslední čas spuštění úlohy.|
|endTime|Datetime|Čas, kdy byla úloha dokončena.|
|exitCode|Int32|Ukončovací kód úkolu|
|retryCount|Int32|Počet opakovaných pokusů o úlohu službou Batch. Úloha se zopakuje, pokud se ukončí s nenulovým ukončovacím kódem, až do zadaného MaxTaskRetryCount.|
|requeueCount|Int32|Počet, kolikrát se služba Batch znovu zařadila do fronty jako výsledek požadavku uživatele.<br /><br /> Když uživatel odebere uzly z fondu (změnou velikosti nebo zmenšení fondu) nebo když je úloha zakázaná, může uživatel určit, že spuštěné úlohy na uzlech se mají znovu zařadit do fronty ke spuštění. Tento počet sleduje, kolikrát se úkol z těchto důvodů znovu zařadil do fronty.|
