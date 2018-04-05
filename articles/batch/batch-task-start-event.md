---
title: Azure události spuštění úlohy Batch | Microsoft Docs
description: Referenční informace pro úlohy Batch spusťte událost.
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
ms.openlocfilehash: 0ad0f87df9db39088769579d538b919b42634c4b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="task-start-event"></a>Událost zahájení úlohy

 Tato událost je vygenerované po úlohu bylo naplánováno spuštění na výpočetním uzlu plánovačem. Upozorňujeme, že pokud je úloha opakovat nebo zařazena Tato událost bude znovu vygenerované pro stejnou úlohu, ale počet opakování, a verze systému úloh bude příslušným způsobem aktualizuje.


 Následující příklad ukazuje, text úlohu spustit událostí.

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

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|JobId|Řetězec|Id úlohy, která obsahuje úlohu.|
|id|Řetězec|Id úkolu.|
|taskType|Řetězec|Typ úlohy. To může být JobManager oznamující, že je úkol správce nebo uživatel oznamující, že se nejedná o úkolu Správce úloh.|
|systemTaskVersion|Int32|Toto je Čítač opakovaných pokusů interní na úlohu. Služba Batch interně může pokus zopakovat úlohu, aby se zohlednily přechodné problémy. Tyto problémy mohou zahrnovat plánování s interními chybami nebo pokusy o obnovení z výpočetních uzlů ve špatném stavu.|
|[nodeInfo](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetním uzlu, na kterém byla úloha spuštěna.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplexní typ|Určuje, že tato úloha je úkol s více instancemi nutnosti několika výpočetních uzlech.  V tématu [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) podrobnosti.|
|[Omezení](#constraints)|Komplexní typ|Provádění omezení, které se vztahují k tomuto úkolu.|
|[executionInfo](#executionInfo)|Komplexní typ|Obsahuje informace o provádění úlohy.|

###  <a name="nodeInfo"></a> nodeInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|poolId|Řetězec|Id fondu, ve kterém byla spuštěna úloha.|
|nodeId|Řetězec|Id uzlu, na kterém byla úloha spuštěna.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|numberOfInstances|Int|Celkový počet výpočetních uzlů požadovaných úkolem|

###  <a name="constraints"></a> Omezení

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Maximální počet, který může být Opakovat úlohu. Služba Batch úkol zopakuje, pokud je jeho ukončovací kód nenulové hodnoty.<br /><br /> Všimněte si, že tato hodnota konkrétně určuje počet opakovaných pokusů. Služba Batch bude opakujte úlohu jednou a mohou zkuste až toto omezení. Například pokud je maximální počet opakování je 3, Batch pokusů a úloh až 4 případech (jeden počáteční pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch neopakuje úlohy.<br /><br /> Pokud je maximální počet opakování −1, služba Batch opakuje úlohy bez omezení.<br /><br /> Výchozí hodnota je 0 (bez opakování).|

###  <a name="executionInfo"></a> executionInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|retryCount|Int32|Počet, kolikrát má byla úloha opakovat službou Batch. Úloha je opakovat, pokud ho ukončí nenulový ukončovací kód, až do zadaného MaxTaskRetryCount|
