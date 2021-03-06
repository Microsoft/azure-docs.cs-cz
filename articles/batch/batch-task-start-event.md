---
title: Událost zahájení úlohy Azure Batch
description: Referenční informace pro událost zahájení úlohy Batch Tato událost je generována, jakmile Scheduler naplánuje spuštění úlohy na výpočetním uzlu.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 3a57ffbb1e1659cff54d101aa4b90ca1bd5d3a57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91851012"
---
# <a name="task-start-event"></a>Událost zahájení úlohy

 Tato událost je generována, jakmile Scheduler naplánuje spuštění úlohy na výpočetním uzlu. Všimněte si, že pokud se úloha zopakuje nebo znovu zařadí do fronty, tato událost se pro stejnou úlohu vygeneruje znovu, ale počet opakování a verze systémové úlohy se odpovídajícím způsobem aktualizuje.


 Následující příklad ukazuje tělo události zahájení úlohy.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
    "requiredSlots": 1,
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
|`jobId`|Řetězec|ID úlohy obsahující úlohu.|
|`id`|Řetězec|ID úkolu|
|`taskType`|Řetězec|Typ úkolu. Může to být buď "JobManager", což značí, že se jedná o úkol správce úloh nebo "uživatel", což značí, že se nejedná o úkol správce úloh.|
|`systemTaskVersion`|Int32|Toto je interní čítač opakování na úkolu. Interně může služba Batch Opakovat úlohu, aby se zohlednila přechodná chyba. Tyto problémy mohou zahrnovat interní chyby plánování nebo se pokusí o zotavení z výpočetních uzlů ve špatném stavu.|
|`requiredSlots`|Int32|Požadované sloty pro spuštění úlohy.|
|[`nodeInfo`](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetním uzlu, na kterém byl úkol spuštěn.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplexní typ|Určuje, že úkol je úloha s více instancemi vyžadující více výpočetních uzlů.  Podrobnosti najdete v tématu [multiInstanceSettings](/rest/api/batchservice/get-information-about-a-task) .|
|[`constraints`](#constraints)|Komplexní typ|Omezení provádění, která se vztahují na tento úkol.|
|[`executionInfo`](#executionInfo)|Komplexní typ|Obsahuje informace o provedení úlohy.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`poolId`|Řetězec|ID fondu, ve kterém byl úkol spuštěn.|
|`nodeId`|Řetězec|ID uzlu, na kterém byl úkol spuštěn.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`numberOfInstances`|Int|Počet výpočetních uzlů vyžadovaných úkolem.|

###  <a name="constraints"></a><a name="constraints"></a> jednotlivým

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maximální počet pokusů, kolikrát může být úloha opakována. Služba Batch opakuje úlohu, pokud je její ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch úlohu zopakuje a potom ji můžete opakovat až do tohoto limitu. Například pokud je maximální počet opakování 3, Batch se pokusí úlohu provést až čtyřikrát (jeden počáteční pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch neopakuje úlohy.<br /><br /> Pokud je maximální počet opakování-1, služba Batch zopakuje úlohy bez omezení.<br /><br /> Výchozí hodnota je 0 (žádné opakování).|

###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`retryCount`|Int32|Počet opakovaných pokusů o úlohu službou Batch. Úloha se zopakuje, pokud se ukončí s nenulovým ukončovacím kódem, až do zadaného MaxTaskRetryCount|
