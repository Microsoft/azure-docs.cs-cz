---
title: Událost dokončení úlohy Azure Batch
description: Referenční informace pro událost dokončení dávkové úlohy Tato událost je generována po dokončení úkolu bez ohledu na ukončovací kód.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 2f591330df4a3757ba1f5c6be1a34955c4cc2fa6
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937737"
---
# <a name="task-complete-event"></a>Událost dokončení úlohy

 Tato událost je generována po dokončení úkolu bez ohledu na ukončovací kód. Tato událost se dá použít k určení doby trvání úkolu, na kterém se úloha spustila, a o tom, jestli se znovu pokusila.


 Následující příklad ukazuje tělo události dokončení úlohy.

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
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`jobId`|Řetězec|ID úlohy obsahující úlohu.|
|`id`|Řetězec|ID úkolu|
|`taskType`|Řetězec|Typ úkolu. Může to být buď "JobManager", což značí, že se jedná o úkol správce úloh nebo "uživatel", což značí, že se nejedná o úkol správce úloh. Tato událost není vygenerována pro úlohy přípravy úlohy, úkoly uvolnění úlohy ani pro úlohy zahájení.|
|`systemTaskVersion`|Datový typ Int32|Toto je interní čítač opakování na úkolu. Interně může služba Batch Opakovat úlohu, aby se zohlednila přechodná chyba. Tyto problémy mohou zahrnovat interní chyby plánování nebo se pokusí o zotavení z výpočetních uzlů ve špatném stavu.|
|[`nodeInfo`](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetním uzlu, na kterém byl úkol spuštěn.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplexní typ|Určuje, že úkol je úloha s více instancemi vyžadující více výpočetních uzlů.  Podrobnosti najdete v tématu [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) .|
|[`constraints`](#constraints)|Komplexní typ|Omezení provádění, která se vztahují na tento úkol.|
|[`executionInfo`](#executionInfo)|Komplexní typ|Obsahuje informace o provedení úlohy.|

###  <a name="nodeInfo"></a>nodeInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`poolId`|Řetězec|ID fondu, ve kterém byl úkol spuštěn.|
|`nodeId`|Řetězec|ID uzlu, na kterém byl úkol spuštěn.|

###  <a name="multiInstanceSettings"></a>multiInstanceSettings

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`numberOfInstances`|Datový typ Int32|Počet výpočetních uzlů vyžadovaných úkolem.|

###  <a name="constraints"></a>jednotlivým

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Datový typ Int32|Maximální počet pokusů, kolikrát může být úloha opakována. Služba Batch opakuje úlohu, pokud je její ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch úlohu zopakuje a potom ji můžete opakovat až do tohoto limitu. Například pokud je maximální počet opakování 3, Batch se pokusí úlohu provést až čtyřikrát (jeden počáteční pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch neopakuje úlohy.<br /><br /> Pokud je maximální počet opakování-1, služba Batch zopakuje úlohy bez omezení.<br /><br /> Výchozí hodnota je 0 (žádné opakování).|

###  <a name="executionInfo"></a>executionInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`startTime`|Datum a čas|Čas spuštění úlohy. ' Running ' odpovídá stavu **spuštěno** , takže pokud úloha určí soubory prostředků nebo balíčky aplikací, pak čas spuštění odráží čas, kdy se úloha spustila stahování nebo nasazování.  Pokud byl úkol restartován nebo opakován, jedná se o poslední čas spuštění úlohy.|
|`endTime`|Datum a čas|Čas, kdy byla úloha dokončena.|
|`exitCode`|Datový typ Int32|Ukončovací kód úkolu|
|`retryCount`|Datový typ Int32|Počet opakovaných pokusů o úlohu službou Batch. Úloha se zopakuje, pokud se ukončí s nenulovým ukončovacím kódem, až do zadaného MaxTaskRetryCount.|
|`requeueCount`|Datový typ Int32|Počet, kolikrát se služba Batch znovu zařadila do fronty jako výsledek požadavku uživatele.<br /><br /> Když uživatel odebere uzly z fondu (změnou velikosti nebo zmenšení fondu) nebo když je úloha zakázaná, může uživatel určit, že spuštěné úlohy na uzlech se mají znovu zařadit do fronty ke spuštění. Tento počet sleduje, kolikrát se úkol z těchto důvodů znovu zařadil do fronty.|
