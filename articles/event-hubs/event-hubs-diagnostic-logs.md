---
title: Nastavení diagnostických protokolů – Azure Event Hub | Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit protokoly aktivit a diagnostické protokoly pro centra událostí v Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 68aa62ad34f8db531d439a581ef024862da0f90c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162306"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Nastavení diagnostických protokolů pro centra událostí Azure

Můžete zobrazit dva typy protokolů pro Azure Event Hubs:

* **[Protokoly aktivit](../azure-monitor/platform/platform-logs-overview.md)**: Tyto protokoly mají informace o operacích prováděných na úloze. Protokoly jsou vždy povoleny.
* **[Diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md)**: Diagnostické protokoly můžete nakonfigurovat pro bohatší zobrazení všeho, co se děje s úlohou. Diagnostické protokoly pokrývají aktivity od okamžiku, kdy je úloha vytvořena, až do odstranění úlohy, včetně aktualizací a aktivit, ke kterým dochází při spuštění úlohy.

## <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů

Diagnostické protokoly jsou ve výchozím nastavení zakázány. Chcete-li povolit diagnostické protokoly, postupujte takto:

1.  Na [webu Azure Portal](https://portal.azure.com)klikněte v části **Monitoring + Management**na **Diagnostics logs**.

    ![Navigace v podokně do diagnostických protokolů](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klikněte na prostředek, který chcete sledovat.

3.  Klikněte na **Zapnout diagnostiku**.

    ![Zapnutí diagnostických protokolů](./media/event-hubs-diagnostic-logs/image2.png)

4.  V **popřípadě stav**klepněte na **tlačítko Zapnuto**.

    ![Změna stavu diagnostických protokolů](./media/event-hubs-diagnostic-logs/image3.png)

5.  Nastavte požadovaný cíl archivu. Například účet úložiště, centrum událostí nebo protokoly Azure Monitor.

6.  Uložte nové nastavení diagnostiky.

Nové nastavení se projeví asi za 10 minut. Poté se protokoly zobrazí v nakonfigurovaném cíli archivace v podokně **Protokoly diagnostiky.**

Další informace o konfiguraci diagnostiky najdete v [přehledu protokolů diagnostiky Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Kategorie diagnostických protokolů

Centra událostí zachycují diagnostické protokoly pro dvě kategorie:

* **Archivní protokoly**: protokoly související s archivy Event Hubs, konkrétně protokoly související s chybami archivu.
* **Provozní protokoly**: informace o tom, co se děje během operací Event Hubs, konkrétně typ operace, včetně vytvoření centra událostí, použité prostředky a stav operace.

## <a name="diagnostic-logs-schema"></a>Schéma diagnostických protokolů

Všechny protokoly jsou uloženy ve formátu JavaScript Object Notation (JSON). Každá položka má řetězcová pole, která používají formát popsaný v následujících částech.

### <a name="archive-logs-schema"></a>Schéma protokolů archivu

Řetězce JSON protokolu archivu obsahují prvky uvedené v následující tabulce:

Name (Název) | Popis
------- | -------
Název_úkolu | Popis úlohy, která se nezdařila.
ActivityId | Interní ID, které se používá pro sledování.
trackingId | Interní ID, které se používá pro sledování.
resourceId | ID prostředku Správce prostředků Azure.
eventHub | Úplný název centra událostí (obsahuje název oboru názvů).
Partitionid | Oddíl Centra událostí se zapisuje.
archivStep | ArchivFlushWriter
startTime | Čas spuštění selhání.
Selhání | Počet případů selhání došlo k selhání.
durationInSeconds | Doba trvání selhání.
zpráva | Chybová zpráva.
category | Archivní logy

Následující kód je příkladem řetězce JSON protokolu archivu:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Schéma provozních protokolů

Řetězce JSON provozního protokolu obsahují prvky uvedené v následující tabulce:

Name (Název) | Popis
------- | -------
ActivityId | Interní ID, které se používá ke sledování účelu.
EventName | Název operace  
resourceId | ID prostředku Správce prostředků Azure.
SubscriptionId | ID předplatného.
EventTimeString | Provozní doba.
Vlastnosti eventu | Vlastnosti operace.
Status | Stav operace.
Volající | Volající operace (portál Azure nebo klient pro správu).
category | Provozní logy

Následující kód je příkladem provozního řetězce JSON protokolu:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Další kroky
- [Úvod do centra událostí](event-hubs-what-is-event-hubs.md)
- [Přehled rozhraní API služby Event Hubs](event-hubs-api-overview.md)
- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
