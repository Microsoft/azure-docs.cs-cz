---
title: Azure App Service jako zdroj Event Grid
description: Tento článek popisuje, jak použít Azure App Service jako zdroj události Event Grid. Poskytuje schéma a odkazy na články týkající se kurzu a postupů.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: jafreebe
ms.openlocfilehash: 224cb44ef7293f47855b5b418830a7fc4bf5ecd1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366649"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události Azure App Service. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). Nabízí také seznam rychlých startů a kurzů pro použití Azure App Service jako zdroje událostí.

## <a name="available-event-types"></a>Dostupné typy událostí

Azure App Service emituje následující typy událostí.

|    Event Type                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft. Web/weby. BackupOperationStarted             |    Aktivované při zahájení zálohování                             |
|    Microsoft. Web/weby. BackupOperationCompleted           |    Aktivované po dokončení zálohování                           |
|    Microsoft. Web/weby. BackupOperationFailed              |    Aktivované při selhání zálohování                              |
|    Microsoft. Web/weby. RestoreOperationStarted            |    Aktivuje se, když se spustí obnovení ze zálohy.        |
|    Microsoft. Web/weby. RestoreOperationCompleted          |    Aktivuje se, když se dokončí obnovení ze zálohy.      |
|    Microsoft. Web/weby. RestoreOperationFailed             |    Aktivuje se, když se nepovedlo obnovení ze zálohy.         |
|    Microsoft. Web/weby. SlotSwapStarted                    |    Aktivuje se při spuštění prohození slotu.                          |
|    Microsoft. Web/weby. SlotSwapCompleted                  |    Aktivuje se, když se dokončí prohození slotu.                      |
|    Microsoft. Web/weby. SlotSwapFailed                     |    Aktivuje se, když se nezdařil swap slot.                           |
|    Microsoft. Web/weby. SlotSwapWithPreviewStarted         |    Aktivuje se, když se spustí swap slot s náhledem.           |
|    Microsoft. Web/weby. SlotSwapWithPreviewCancelled       |    Aktivuje se, když se zruší výměna slotu s náhledem.    |
|    Microsoft. Web/weby. AppUpdated. restartuje se.               |    Aktivuje se při restartování lokality.                      |
|    Microsoft. Web/weby. AppUpdated. Stopped                 |    Aktivuje se, když se web zastavil.                          |
|    Microsoft. Web/weby. AppUpdated.ChangedAppSettings      |    Aktivuje se, když se změní nastavení aplikace webu.             |
|    Microsoft. Web/serverových farem. AppServicePlanUpdated        |    Aktivuje se při aktualizaci plánu App Service.                 |

## <a name="properties-common-to-all-events"></a>Vlastnosti společné pro všechny události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)
Když se aktivuje událost, Služba Event Grid odešle data o této události do předplatného koncového bodu.
V této části najdete příklad toho, jak by tato data vypadala jako u každé události. Každá událost má následující data nejvyšší úrovně:

|     Vlastnost          |     Typ     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    řetězec    |    Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid.                                      |
|    `subject`            |    řetězec    |    Cesta definovaná vydavatelem k předmětu události                                                                                              |
|    `eventType`          |    řetězec    |    Jeden z registrovaných typů událostí pro tento zdroj události.                                                                                  |
|    `eventTime`          |    řetězec    |    Čas, kdy se událost generuje na základě času UTC poskytovatele.                                                                         |
|    `id`                 |    řetězec    |    Jedinečný identifikátor události                                                                                                            |
|    `data`               |    object    |    Data události služby Blob Storage.                                                                                                                    |
|    `dataVersion`        |    řetězec    |    Verze schématu datového objektu. Verzi schématu definuje vydavatel.                                                          |
|    `metadataVersion`    |    řetězec    |    Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid.    |

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Když se aktivuje událost, Služba Event Grid odešle data o této události do předplatného koncového bodu.
V této části najdete příklad toho, jak by tato data vypadala jako u každé události. Každá událost má následující data nejvyšší úrovně:

|     Vlastnost          |     Typ     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    řetězec    |    Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid.                                      |
|    `subject`            |    řetězec    |    Cesta definovaná vydavatelem k předmětu události                                                                                              |
|    `type`          |    řetězec    |    Jeden z registrovaných typů událostí pro tento zdroj události.                                                                                  |
|    `time`          |    řetězec    |    Čas, kdy se událost generuje na základě času UTC poskytovatele.                                                                         |
|    `id`                 |    řetězec    |    Jedinečný identifikátor události                                                                                                            |
|    `data`               |    object    |    Data události služby Blob Storage.                                                                                                                    |
| `specversion` | řetězec | Verze specifikace schématu CloudEvents |

---

## <a name="example-events"></a>Příklady událostí

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Podrobnosti o akci u aplikace                                                                                       |
|    `action`                  |    řetězec    |    Typ akce operace                                                                                   |
|    `name`                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    `clientRequestId`         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    `correlationRequestId`    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    `requestId`               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    `address`                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    `verb`                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Podrobnosti o akci u aplikace                                                                                       |
|    `action`                  |    řetězec    |    Typ akce operace                                                                                   |
|    `name`                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    `clientRequestId`         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    `correlationRequestId`    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    `requestId`               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    `address`                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    `verb`                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Podrobnosti o akci u aplikace                                                                                       |
|    `action`                 |    řetězec    |    Typ akce operace                                                                                   |
|    `name`                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    `clientRequestId`         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    `correlationRequestId`    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|   `requestId`               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    `address`                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    `verb`                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |
|    `sourceSlot`              |    řetězec    |    Zdrojový slot prohození                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Podrobnosti o akci u aplikace                                                                                       |
|    `action`                 |    řetězec    |    Typ akce operace                                                                                   |
|    `name`                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    `clientRequestId`         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    `correlationRequestId`    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    `requestId`               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    `address`                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    `verb`                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. restarted, AppUpdated. Stopped, AppUpdated. ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "siteName": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "siteName": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Datový objekt má následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Podrobnosti o akci u aplikace                                                                                       |
|    `action`                  |    řetězec    |    Typ akce operace                                                                                   |
|    `name`                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    `clientRequestId`         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    `correlationRequestId`    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    `requestId`               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    `address`                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    `verb`                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Serverových farem. AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

Datový objekt má následující vlastnosti:

|    Vlastnost                         |    Typ      |    Description                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    object    |    Podrobnosti o akci v plánu služby App Service                                                                          |
|    `stampKind`                        |    řetězec    |    Druh prostředí, ve kterém je plán služby App Service                                                                     |
|    `action`                           |    řetězec    |    Typ akce v plánu služby App Service                                                                            |
|    `status`                           |    řetězec    |    Stav operace v plánu služby App Service                                                                   |
|    `sku`                              |    object    |    SKU plánu služby App Service                                                                                       |
|    `name`                             |    řetězec    |    název plánu služby App Service                                                                                      |
|    `Tier`                             |    řetězec    |    úroveň plánu služby App Service                                                                                      |
|    `Size`                             |    řetězec    |    velikost plánu služby App Service                                                                                      |
|    `Family`                           |    řetězec    |    řada plánu služby App Service                                                                                        |
|    `Capacity`                         |    řetězec    |    kapacita plánu služby App Service                                                                                      |
|    `action`                           |    řetězec    |    Typ akce operace                                                                                   |
|    `name`                             |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    `clientRequestId`                  |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    `correlationRequestId`             |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    `requestId`                        |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    `address`                         |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    `verb`                             |    řetězec    |    Příkaz HTTP této operace                                                                                       |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md) .