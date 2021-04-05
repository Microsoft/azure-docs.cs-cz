---
title: Událost selhání plánu úlohy Azure Batch
description: Referenční informace pro událost selhání plánu úlohy Batch Tato událost se vygeneruje, když se úloha nepovede naplánovat a později se zopakuje.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852140"
---
# <a name="task-schedule-fail-event"></a>Událost selhání plánu úloh

 Tato událost je generována v případě, že se úloha nezdařila a bude opakována později. Jedná se o dočasnou chybu v době plánování úkolu v důsledku omezení prostředků, například nedostatek slotů dostupných na uzlech ke spuštění úlohy se `requiredSlots` zadaným.

 Následující příklad ukazuje tělo události selhání plánu úkolu.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`jobId`|Řetězec|ID úlohy obsahující úlohu.|
|`id`|Řetězec|ID úkolu|
|`taskType`|Řetězec|Typ úkolu. Může to být buď "JobManager", což značí, že se jedná o úkol správce úloh nebo "uživatel", což značí, že se nejedná o úkol správce úloh. Tato událost není vygenerována pro úlohy přípravy úlohy, úkoly uvolnění úlohy ani pro úlohy zahájení.|
|`systemTaskVersion`|Int32|Toto je interní čítač opakování na úkolu. Interně může služba Batch Opakovat úlohu, aby se zohlednila přechodná chyba. Tyto problémy mohou zahrnovat interní chyby plánování nebo se pokusí o zotavení z výpočetních uzlů ve špatném stavu.|
|`requiredSlots`|Int32|Požadované sloty pro spuštění úlohy.|
|[`nodeInfo`](#nodeInfo)|Komplexní typ|Obsahuje informace o výpočetním uzlu, na kterém byl úkol spuštěn.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplexní typ|Určuje, že úkol je úloha s více instancemi vyžadující více výpočetních uzlů.  Podrobnosti najdete v tématu [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task) .|
|[`constraints`](#constraints)|Komplexní typ|Omezení provádění, která se vztahují na tento úkol.|
|[`schedulingError`](#schedulingError)|Komplexní typ|Obsahuje informace o chybě plánování úkolu.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`poolId`|Řetězec|ID fondu, ve kterém byl úkol spuštěn.|
|`nodeId`|Řetězec|ID uzlu, na kterém byl úkol spuštěn.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Počet výpočetních uzlů vyžadovaných úkolem.|

###  <a name="constraints"></a><a name="constraints"></a> jednotlivým

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Maximální počet pokusů, kolikrát může být úloha opakována. Služba Batch opakuje úlohu, pokud je její ukončovací kód nenulový.<br /><br /> Všimněte si, že tato hodnota konkrétně řídí počet opakování. Služba Batch úlohu zopakuje a potom ji můžete opakovat až do tohoto limitu. Například pokud je maximální počet opakování 3, Batch se pokusí úlohu provést až čtyřikrát (jeden počáteční pokus a 3 opakování).<br /><br /> Pokud je maximální počet opakování 0, služba Batch neopakuje úlohy.<br /><br /> Pokud je maximální počet opakování-1, služba Batch zopakuje úlohy bez omezení.<br /><br /> Výchozí hodnota je 0 (žádné opakování).|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`category`|Řetězec|Kategorie chyby|
|`code`|Řetězec|Identifikátor chyby plánování úlohy. Kódy jsou invariantní a mají být zpracovány programově.|
|`message`|Řetězec|Zpráva popisující chybu plánování úkolů, která má být vhodná pro zobrazení v uživatelském rozhraní.|
