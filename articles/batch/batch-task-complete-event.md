---
title: Azure událost dokončení úlohy Batch | Dokumentace Microsoftu
description: Referenční informace pro událost dokončení úlohy služby Batch.
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
ms.openlocfilehash: b5fd1a8020c8e95323bc2333c0583dafe58e8456
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459236"
---
# <a name="task-complete-event"></a>Událost dokončení úlohy

 Tato událost je vygenerován, jakmile se dokončí úlohu, bez ohledu na to, ukončovací kód. Tuto událost lze použít k určení doby trvání úkolu, pokud se úkol spustil, a zda byla opakována.


 Následující příklad ukazuje tělo událost dokončení úlohy.

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
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|jobId|String|Id úlohy obsahující úlohu.|
|id|String|Identifikátor úkolu|
|taskType|String|Typ úlohy. To může být JobManager oznamující, že je úkol Správce úloh nebo uživatel oznamující, že se nejedná o úkol Správce úloh. Tato událost není aktivováno, úkolů přípravy úloh, uvolnění úloh nebo spuštění úlohy.|
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
|numberOfInstances|Int32|Celkový počet výpočetních uzlů požadovaných úkolem|

###  <a name="constraints"></a> Omezení

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maximální počet pokusů, které se úkol neopakuje. Služba Batch úkol zopakuje, pokud je jeho ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch úkol pokusí jednou a může opakujte až toto omezení. Například pokud se maximální počet opakování 3 pokusy Batch úkol, který je až 4 krát (jeden první pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch úkoly neopakuje.<br /><br /> Pokud je maximální počet opakování -1, služba Batch opakuje bez omezení.<br /><br /> Výchozí hodnota je 0 (žádná opakování).|

###  <a name="executionInfo"></a> executionInfo

|Název elementu|Type|Poznámky|
|------------------|----------|-----------|
|startTime|DateTime|Doba, jakou úloha spuštěna. "Spuštěna" odpovídá **systémem** stavu, takže pokud úkol Určuje soubory prostředků nebo balíčky aplikací, pak počáteční čas odráží čas, kdy úloha spuštěna, stahování nebo jejich nasazení.  Pokud úloha byl restartován nebo opakovat, toto je nejnovější čas, kdy úloha spuštěna.|
|endTime|DateTime|Čas, kdy úloha dokončena.|
|exitCode|Int32|Ukončovací kód úkolu.|
|retryCount|Int32|Počet pokusů, které úlohy se pokus o pomocí služby Batch. Úloha je opakovat, pokud ho ukončí s nenulový ukončovací kód, až do zadaného MaxTaskRetryCount.|
|requeueCount|Int32|Počet pokusů, které úloha má byla znovu zařadit do fronty pomocí služby Batch jako výsledek požadavku uživatele.<br /><br /> Když uživatel odebere uzly z fondu (nebo změnou velikosti zmenšit fond) nebo při deaktivaci úlohy, uživatel může určit, že spuštěné úkoly na uzlech se zařadí do fronty pro spuštění. Tento počet sleduje, kolikrát úkol má byla znovu zařadit do fronty z těchto důvodů.|
