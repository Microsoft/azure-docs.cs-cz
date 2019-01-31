---
title: Událost zahájení úlohy služby Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro událost zahájení úlohy služby Batch.
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
ms.openlocfilehash: d50a0a7082e409084fd966370934a638ca9bb013
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474424"
---
# <a name="task-start-event"></a>Událost zahájení úlohy

 Tato událost je vygenerován, jakmile se úloha je naplánovaná na spuštění na výpočetních uzlech plánovačem. Všimněte si, že pokud je úloha opakovat, nebo znovu zařadit do fronty Tato událost se znovu vygenerován pro stejný úkol, ale počet opakování a verze úlohy systému se podle nich aktualizuje.


 Následující příklad ukazuje tělo úkolu počáteční událost.

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
        "retryCount": 0
    }
}
```

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|jobId|String|Id úlohy obsahující úlohu.|
|id|String|Identifikátor úkolu|
|taskType|String|Typ úlohy. To může být JobManager oznamující, že je úkol Správce úloh nebo uživatel oznamující, že se nejedná o úkol Správce úloh.|
|systemTaskVersion|Int32|Toto je čítač interní opakovaných pokusů pro úlohu. Interně služba Batch může pokus zopakovat úlohu, aby se zohlednily přechodné problémy. Tyto problémy mohou zahrnovat plánování s interními chybami nebo pokusy o obnovení z výpočetních uzlů ve špatném stavu.|
|[nodeInfo](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetním uzlu, na kterém se úkol spustil.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplexní typ|Určuje, že je úloha úkolu víc instancí vyžaduje víc výpočetních uzlů.  Zobrazit [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) podrobnosti.|
|[Omezení](#constraints)|Komplexní typ|Omezení zpracování, které se vztahují k tomuto úkolu.|
|[executionInfo](#executionInfo)|Komplexní typ|Obsahuje informace o provádění úkolu.|

###  <a name="nodeInfo"></a> nodeInfo

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|poolId|String|Id fondu, ve kterém se úkol spustil.|
|nodeId|String|Id uzlu, na kterém se úkol spustil.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|numberOfInstances|Int|Celkový počet výpočetních uzlů požadovaných úkolem|

###  <a name="constraints"></a> Omezení

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maximální počet pokusů, které se úkol neopakuje. Služba Batch úkol zopakuje, pokud je jeho ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch úkol pokusí jednou a může opakujte až toto omezení. Například pokud se maximální počet opakování 3 pokusy Batch úkol, který je až 4 krát (jeden první pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch úkoly neopakuje.<br /><br /> Pokud je maximální počet opakování -1, služba Batch opakuje bez omezení.<br /><br /> Výchozí hodnota je 0 (žádná opakování).|

###  <a name="executionInfo"></a> executionInfo

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|retryCount|Int32|Počet pokusů, které úlohy se pokus o pomocí služby Batch. Úloha je opakovat, pokud ukončen s nenulový ukončovací kód, až do zadaného MaxTaskRetryCount|
