---
title: Nastavení diagnostických protokolů – centrum událostí Azure | Microsoft Docs
description: Naučte se, jak nastavit protokoly aktivit a diagnostické protokoly pro centra událostí v Azure.
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
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509759"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Nastavení diagnostických protokolů pro centra událostí Azure

Můžete si zobrazit dva typy protokolů pro Azure Event Hubs:

* **[Protokoly aktivit](../azure-monitor/platform/platform-logs-overview.md)**: tyto protokoly obsahují informace o operacích provedených v rámci úlohy. Protokoly jsou vždycky povolené. Položky protokolu aktivit můžete zobrazit tak, že v levém podokně pro obor názvů centra událostí v Azure Portal vyberete **Protokol aktivit** . Například: "vytvořit nebo aktualizovat obor názvů", "vytvořit nebo aktualizovat centrum událostí".

    ![Protokol aktivit pro obor názvů Event Hubs](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md)**: můžete nakonfigurovat diagnostické protokoly pro rozsáhlejší zobrazení všeho, co se stane s úlohou. Diagnostické protokoly zahrnují aktivity z doby, kdy se úloha vytvoří, až do odstranění úlohy, včetně aktualizací a aktivit, ke kterým dojde, když je úloha spuštěná.

    V následující části se dozvíte, jak povolit diagnostické protokoly pro obor názvů Event Hubs.

## <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů
Diagnostické protokoly jsou ve výchozím nastavení zakázané. K povolení diagnostických protokolů použijte následující postup:

1.  V [Azure Portal](https://portal.azure.com)přejděte do svého oboru názvů Event Hubs. 
2. V levém podokně vyberte **nastavení diagnostiky** v části **monitorování** a pak vyberte **+ Přidat nastavení diagnostiky**. 

    ![Stránka nastavení diagnostiky – přidat nastavení diagnostiky](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. V části **Podrobnosti kategorie** vyberte **typy diagnostických protokolů** , které chcete povolit. Podrobnosti o těchto kategoriích najdete dále v tomto článku. 
5. V části **Podrobnosti o cíli** nastavte cíl archivu (cíl), který chcete. například účet úložiště, centrum událostí nebo Log Analytics pracovní prostor.

    ![Přidat stránku nastavení diagnostiky](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Kliknutím na tlačítko **Uložit** na panelu nástrojů uložte nastavení diagnostiky.

    Nové nastavení se projeví přibližně po dobu 10 minut. Pak se protokoly objeví v nakonfigurovaném cíli archivace v podokně **diagnostické protokoly** .

    Další informace o konfiguraci diagnostiky najdete v tématu [Přehled diagnostických protokolů Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Kategorie diagnostických protokolů

Event Hubs zachycuje diagnostické protokoly pro následující kategorie:

- **Protokoly archivu**: protokoly související s Event Hubs archivy, konkrétně protokoly týkající se chyb archivu.
- **Provozní protokoly**: informace o tom, co se děje během operací Event Hubs, konkrétně typ operace, včetně vytvoření centra událostí, použitých prostředků a stavu operace.
- **Protokoly automatického škálování**: informace o operacích automatického škálování se provádí v oboru názvů Event Hubs. 
- **Protokoly koordinátora Kafka** – informace o operacích koordinátora Kafka souvisejících s Event Hubs. 
- **Kafka User logs**: informace o uživatelských operacích Kafka souvisejících s Event Hubs. 
- **Událost připojení Event Hubs virtuální sítě (VNET)**: informace o Event Hubs událostech připojení k virtuální síti. 
- **Klíčové uživatelské protokoly spravované zákazníkem**: informace o operacích souvisejících s klíčem spravovaným zákazníkem. 


    Všechny protokoly jsou uložené ve formátu JavaScript Object Notation (JSON). Každá položka má pole řetězce, která používají formát popsaný v následujících částech.

## <a name="archive-logs-schema"></a>Schéma protokolů archivu

Řetězce JSON protokolu archivu obsahují prvky uvedené v následující tabulce:

Název | Popis
------- | -------
/TN | Popis úlohy, která selhala.
ActivityId | Interní ID, které se používá ke sledování.
trackingId | Interní ID, které se používá ke sledování.
resourceId | ID prostředku Azure Resource Manager.
eventHub | Úplný název centra událostí (zahrnuje název oboru názvů).
Oddílu | Probíhá zápis do oddílu centra událostí.
archiveStep | ArchiveFlushWriter
startTime | Čas spuštění se nezdařil.
úspěšně | Počet výskytů chyby.
durationInSeconds | Doba, po kterou došlo k chybě.
zpráva | Chybová zpráva
category | ArchiveLogs

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

## <a name="operational-logs-schema"></a>Schéma provozních protokolů

Řetězce JSON provozního protokolu obsahují prvky uvedené v následující tabulce:

Název | Popis
------- | -------
ActivityId | Interní ID, které slouží ke sledování účelu.
EventName | Název operace  
resourceId | ID prostředku Azure Resource Manager.
SubscriptionId | ID předplatného.
EventTimeString | Operační čas.
EventProperties | Vlastnosti operace.
Status | Stav operace.
Volající | Volající operace (Azure Portal nebo klient pro správu).
category | OperationalLogs

Následující kód je příkladem řetězce JSON provozního protokolu:

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

## <a name="autoscale-logs-schema"></a>Schéma protokolů automatického škálování
JSON protokolu automatického škálování obsahuje prvky uvedené v následující tabulce:

| Název | Popis |
| ---- | ----------- | 
| trackingId | Interní ID, které se používá pro účely trasování |
| resourceId | Interní ID, které obsahuje ID předplatného Azure a název oboru názvů |
| zpráva | Informační zpráva, která poskytuje podrobné informace o automatické neploché akci. Zpráva obsahuje předchozí a aktuální hodnotu jednotky propustnosti pro daný obor názvů a, která aktivovala neplochý počet výskytů. |

## <a name="kafka-coordinator-logs-schema"></a>Schéma protokolů koordinátora Kafka
JSON protokolu Kafka Coordinator obsahuje prvky uvedené v následující tabulce:

| Název | Popis |
| ---- | ----------- | 
| Identifikátor | ID žádosti, která se používá pro účely trasování |
| resourceId | Interní ID, které obsahuje ID předplatného Azure a název oboru názvů |
| operationName | Název operace, která se má provést během koordinace skupiny |
| clientId | ID klienta |
| namespaceName | Název oboru názvů | 
| subscriptionId | ID předplatného Azure |
| zpráva | Informační zpráva, která poskytuje podrobné informace o akcích provedených během koordinace skupiny uživatelů. |

## <a name="kafka-user-error-logs-schema"></a>Schéma protokolů chyb uživatele Kafka
JSON protokolu chyb uživatele Kafka obsahuje prvky uvedené v následující tabulce:

| Název | Popis |
| ---- | ----------- |
| trackingId | ID sledování, které se používá pro účely trasování. |
| namespaceName | Název oboru názvů |
| eventhub | Název centra událostí |
| Oddílu | ID oddílu |
| groupId | ID skupiny |
| ClientId | ID klienta |
| resourceId | Interní ID, které obsahuje ID předplatného Azure a název oboru názvů |
| zpráva | Informační zpráva, která poskytuje podrobné informace o chybě |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs schéma událostí připojení k virtuální síti

Event Hubs JSON události připojení virtuální sítě (VNet) obsahuje prvky uvedené v následující tabulce:

| Název | Popis |
| ---  | ----------- | 
| subscriptionId | ID předplatného Azure |
| namespaceName | Název oboru názvů |
| Adresa | IP adresa klienta připojujícího se ke službě Event Hubs |
| action | Akce prováděná službou Event Hubs při vyhodnocování požadavků na připojení. Podporované akce jsou **AcceptConnection** a **RejectConnection**. |
| reason | Poskytuje důvod, proč byla akce dokončena. |
| count | Počet výskytů pro danou akci |
| resourceId | ID interního prostředku, které obsahuje ID předplatného a název oboru názvů. |

## <a name="customer-managed-key-user-logs"></a>Klíčové uživatelské protokoly spravované uživatelem
Kód JSON klíče uživatele spravovaný klíčem zákazníka obsahuje prvky uvedené v následující tabulce:

| Název | Popis |
| ---- | ----------- | 
| category | Typ kategorie pro zprávu Je to jedna z následujících hodnot: **Chyba** a **informace** |
| resourceId | ID interního prostředku, což zahrnuje ID předplatného Azure a název oboru názvů |
| keyVault | Název prostředku Key Vault |
| key | Název Key Vaultho klíče |
| version | Verze Key Vaultho klíče |
| NázevOperace | Název operace, která byla provedena k obsluze požadavků |
| kód | Kód stavu |
| zpráva | Zpráva, která poskytuje podrobné informace o chybě nebo informativní zprávě |



## <a name="next-steps"></a>Další kroky
- [Úvod do Event Hubs](event-hubs-what-is-event-hubs.md)
- [Přehled rozhraní API služby Event Hubs](event-hubs-api-overview.md)
- Začínáme se službou Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
