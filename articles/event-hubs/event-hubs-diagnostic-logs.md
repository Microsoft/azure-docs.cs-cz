---
title: Diagnostické protokoly služby Azure Event Hubs | Microsoft Docs
description: Zjistěte, jak nastavit diagnostické protokoly pro event hubs v Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: banisadr
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 451fc42f573db2b60985912cfa63617e04f09e6c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28932711"
---
# <a name="event-hubs-diagnostic-logs"></a>Diagnostické protokoly událostí rozbočovače

Pro Azure Event Hubs můžete zobrazit dva typy protokolů:

* **[Protokoly aktivity](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**: tyto protokoly neobsahuje informace o operacích na úlohu provést. Protokoly jsou vždy povolena.
* **[Diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**: můžete nakonfigurovat diagnostické protokoly pro širší zobrazení všechno, co se děje s úlohou. Diagnostické protokoly titulní aktivit od okamžiku, kdy úloha je vytvořena až do odstranění úlohy, včetně aktualizací a aktivity, ke kterým dochází při běhu úlohy.

## <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů

Diagnostické protokoly jsou zakázané ve výchozím nastavení. Povolení diagnostických protokolů:

1.  V [portál Azure](https://portal.azure.com)v části **monitorování + správu**, klikněte na tlačítko **protokolů diagnostiky**.

    ![Navigační podokno k diagnostickým protokolům](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klikněte na prostředek, který chcete monitorovat.

3.  Klikněte na tlačítko **zapněte diagnostiku**.

    ![Zapnout diagnostické protokoly](./media/event-hubs-diagnostic-logs/image2.png)

4.  Pro **stav**, klikněte na tlačítko **na**.

    ![Změnit stav diagnostických protokolů](./media/event-hubs-diagnostic-logs/image3.png)

5.  Nastavit cíl archiv, který chcete zjistit. například účet úložiště, centra událostí nebo analýza protokolů Azure.

6.  Uložte nové nastavení diagnostiky.

Nové nastavení se projeví ve přibližně 10 minut. Potom protokolů se objeví v nakonfigurovaných archivace cíl, v **protokolů diagnostiky** podokně.

Další informace o konfiguraci diagnostiky, najdete v článku [přehled Azure diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Diagnostické protokoly kategorií

Centra událostí jsou zaznamenány diagnostické protokoly pro dvou kategorií:

* **Protokoly archivovat**: související s archivy Event Hubs, konkrétně protokoly, protokoly související k archivaci chyby.
* **Operační protokoly**: informace o tom, co se děje během operace služby Event Hubs, konkrétně operace typ, včetně vytváření centra událostí, prostředky využívané a stav operace.

## <a name="diagnostic-logs-schema"></a>Diagnostické protokoly schématu

Všechny protokoly se ukládají ve formátu JavaScript Object Notation (JSON). Každá položka má polí s řetězcem, které používají formát popsané v následujících částech.

### <a name="archive-logs-schema"></a>Schéma protokoly archivu

Řetězce formátu JSON protokolu archivu obsahovat prvky uvedené v následující tabulce:

Název | Popis
------- | -------
TaskName | Popis úloha, která se nezdařila.
ID aktivity | Interní ID použité pro sledování.
trackingId | Interní ID použité pro sledování.
resourceId | Azure Resource Manager ID prostředku.
eventHub | Centra událostí celý název (včetně oboru názvů).
ID oddílu | Zapisuje do oddílu centra událostí.
archiveStep | ArchiveFlushWriter
startTime | Selhání spuštění.
selhání | Počet časy, kdy došlo k chybě.
durationInSeconds | Doba trvání selhání.
zpráva | Chybová zpráva.
category | ArchiveLogs

Následující kód je příkladem protokol archivu řetězce formátu JSON:

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

### <a name="operational-logs-schema"></a>Schéma operační protokoly

Řetězce formátu JSON operační protokol obsahovat prvky uvedené v následující tabulce:

Název | Popis
------- | -------
ID aktivity | Interní ID použít ke sledování účel.
EventName | Název operace.  
resourceId | Azure Resource Manager ID prostředku.
SubscriptionId | ID předplatného.
EventTimeString | Operace čas.
EventProperties | Vlastnosti operace.
Status | Stav operace.
Volající | Volající operace (Azure portal nebo správu klienta).
category | OperationalLogs

Následující kód je příkladem řetězce JSON operační protokol:

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
