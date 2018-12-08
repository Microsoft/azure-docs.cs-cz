---
title: Nastavení diagnostické protokoly - Azure Event Hubs | Dokumentace Microsoftu
description: Zjistěte, jak nastavit protokoly aktivit a diagnostické protokoly pro službu event hubs v Azure.
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
ms.openlocfilehash: c0c8740648ad845b58ff0578607f4b5e9c235166
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105474"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Nastavení diagnostické protokoly pro centra událostí Azure

Dva typy protokolů můžete zobrazit pro Azure Event Hubs:

* **[Protokoly aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**: tyto protokoly jsou informace o operace provedené na úlohu. Protokoly jsou vždy povoleny.
* **[Diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**: můžete nakonfigurovat diagnostické protokoly pro bohatší zobrazení všechno, co se děje s úlohou. Diagnostické protokoly aktivit titulní od okamžiku vytvoření úlohy, až do odstranění úlohy, včetně aktualizací a aktivity, ke kterým dochází, když úloha běží.

## <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů

Diagnostické protokoly jsou ve výchozím nastavení zakázané. Povolení diagnostických protokolů, postupujte podle těchto kroků:

1.  V [webu Azure portal](https://portal.azure.com)v části **monitorování a správa**, klikněte na tlačítko **diagnostické protokoly**.

    ![Navigační podokno k diagnostickým protokolům](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klikněte na prostředek, který chcete monitorovat.

3.  Klikněte na **Zapnout diagnostiku**.

    ![Zapnout diagnostické protokoly](./media/event-hubs-diagnostic-logs/image2.png)

4.  Pro **stav**, klikněte na tlačítko **na**.

    ![Změnit stav diagnostické protokoly](./media/event-hubs-diagnostic-logs/image3.png)

5.  Nastavení cíle archiv, který chcete zjistit. například účet úložiště, Centrum událostí nebo Azure Log Analytics.

6.  Uložte nové nastavení diagnostiky.

Nové nastavení se projeví během 10 minut. Potom protokolů se objeví v nakonfigurovaných archivace cíli v **diagnostické protokoly** podokně.

Další informace o konfiguraci diagnostiky, najdete v článku [přehled diagnostické protokoly Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Kategorie pro diagnostické protokoly

Event Hubs shromažďuje diagnostických protokolů pro dvou kategorií:

* **Archivovat protokoly**: související s Event Hubs Archive, konkrétně protokoly, archivovat chyby související s protokoly.
* **Operační protokoly**: informace o tom, co se děje během operace služby Event Hubs, konkrétně typ operace, včetně vytvoření centra událostí, prostředky a stav operace.

## <a name="diagnostic-logs-schema"></a>Diagnostické protokoly schématu

Všechny protokoly se ukládají ve formátu JavaScript Object Notation (JSON). Každý záznam obsahuje pole řetězců, které používají formát je popsáno v následujících částech.

### <a name="archive-logs-schema"></a>Protokoly schématu archivu

Archivace protokolu JSON řetězce obsahovat prvky uvedené v následující tabulce:

Název | Popis
------- | -------
TaskName | Popis úkolu, který se nezdařilo.
ID aktivity | Interní ID pro sledování.
trackingId | Interní ID pro sledování.
resourceId | ID prostředku Azure Resource Manageru
centra událostí | Centrum událostí úplný název (včetně názvu oboru názvů).
ID oddílu | Do oddílu centra událostí.
archiveStep | ArchiveFlushWriter
startTime | Selhání spuštění.
selhání | Počet pokusů, ke které došlo k chybě.
durationInSeconds | Doba trvání selhání.
zpráva | Chybová zpráva.
category | ArchiveLogs

Následující kód je příkladem protokolu archivu řetězec formátu JSON:

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

### <a name="operational-logs-schema"></a>Schéma provozní protokoly

Řetězce JSON operační protokol obsahovat prvky uvedené v následující tabulce:

Název | Popis
------- | -------
ID aktivity | Interní ID používají ke sledování účel.
EventName | Název operace.  
resourceId | ID prostředku Azure Resource Manageru
SubscriptionId | ID předplatného.
EventTimeString | Čas operace.
EventProperties | Vlastnosti operace.
Status | Stav operace.
Volající | Volající operace (Azure portal nebo správy klienta).
category | OperationalLogs

Následující kód je příklad řetězce JSON operační protokol:

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

## <a name="next-steps"></a>Další postup
* [Úvod do služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Přehled rozhraní API služby Event Hubs](event-hubs-api-overview.md)
* [Začínáme s Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
