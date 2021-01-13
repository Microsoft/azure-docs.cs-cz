---
title: Nastavení diagnostických protokolů – centrum událostí Azure | Microsoft Docs
description: Naučte se, jak nastavit protokoly aktivit a diagnostické protokoly pro centra událostí v Azure.
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: 015814b9a56ec963f5209f971f096ac6c173d7e1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131980"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Nastavení diagnostických protokolů pro centra událostí Azure

Můžete si zobrazit dva typy protokolů pro Azure Event Hubs:

* **[Protokoly aktivit](../azure-monitor/platform/platform-logs-overview.md)**: tyto protokoly obsahují informace o operacích provedených v rámci úlohy. Protokoly jsou vždycky povolené. Položky protokolu aktivit můžete zobrazit tak, že v levém podokně pro obor názvů centra událostí v Azure Portal vyberete **Protokol aktivit** . Například: "vytvořit nebo aktualizovat obor názvů", "vytvořit nebo aktualizovat centrum událostí".

    ![Protokol aktivit pro obor názvů Event Hubs](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md)**: diagnostické protokoly poskytují bohatší informace o operacích a akcích, které se provádí v rámci vašeho oboru názvů pomocí rozhraní API, nebo prostřednictvím klientů pro správu v jazykové sadě SDK. 
    
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

| Kategorie | Popis | 
| -------- | ----------- | 
| Protokoly archivu | Zachycuje informace o [Event Hubs operací zachycení](event-hubs-capture-overview.md) , konkrétně o protokolech souvisejících s chybami zachycení. |
| Provozní protokoly | Zachyťte všechny operace správy, které se provádějí v oboru názvů Azure Event Hubs. Datové operace se nezachycují kvůli vysokému objemu operací s daty, které jsou prováděny na Azure Event Hubs. |
| Protokoly automatického škálování | Zachycuje automatické neploché operace provedené v oboru názvů Event Hubs. |
| Protokoly koordinátora Kafka | Zachycuje operace koordinátora Kafka související s Event Hubs. |
| Protokoly chyb uživatele Kafka | Zachycuje informace o rozhraních API Kafka volaných na Event Hubs. |
| Událost připojení k virtuální síti Event Hubs (VNet) | Zachycuje informace o IP adresách a virtuálních sítích odesílajících data do Event Hubs. |
| Klíčové uživatelské protokoly spravované uživatelem | Zachycuje operace související s klíčem spravovaným zákazníkem. |


Všechny protokoly jsou uložené ve formátu JavaScript Object Notation (JSON). Každá položka má pole řetězce, která používají formát popsaný v následujících částech.

## <a name="archive-logs-schema"></a>Schéma protokolů archivu

Řetězce JSON protokolu archivu obsahují prvky uvedené v následující tabulce:

Název | Description
------- | -------
`TaskName` | Popis úlohy, která se nezdařila
`ActivityId` | Interní ID, které se používá ke sledování
`trackingId` | Interní ID, které se používá ke sledování
`resourceId` | ID prostředku Azure Resource Manager
`eventHub` | Úplný název centra událostí (zahrnuje název oboru názvů)
`partitionId` | Oddíl centra událostí, do kterého se zapisuje
`archiveStep` | možné hodnoty: ArchiveFlushWriter, DestinationInit
`startTime` | Čas spuštění chyby
`failures` | Počet výskytů chyby
`durationInSeconds` | Doba trvání selhání
`message` | Chybová zpráva
`category` | ArchiveLogs

Následující kód je příkladem řetězce JSON protokolu archivu:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
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

Název | Description
------- | -------
`ActivityId` | Interní ID, které se používá pro účely sledování |
`EventName` | Název operace Seznam hodnot pro tento prvek najdete v tématu [názvy událostí](#event-names) . |
`resourceId` | ID prostředku Azure Resource Manager |
`SubscriptionId` | ID předplatného |
`EventTimeString` | Čas operace |
`EventProperties` |Vlastnosti pro operaci. Tento prvek poskytuje další informace o události, jak je znázorněno v následujícím příkladu. |
`Status` | Stav operace. Hodnota může být buď **úspěšná** , nebo **neúspěšná**.  |
`Caller` | Volající operace (Azure Portal nebo klient pro správu) |
`Category` | OperationalLogs |

Následující kód je příkladem řetězce JSON provozního protokolu:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

### <a name="event-names"></a>Názvy událostí
Název události je vyplněný jako typ operace + typ prostředku z následujících výčtů. Například,, `Create Queue` `Retrieve Event Hu` nebo `Delete Rule` . 

| Typ operace | Typ prostředku | 
| -------------- | ------------- | 
| <ul><li>Vytvořit</li><li>Aktualizace</li><li>Odstranit</li><li>Stahovat</li><li>Neznámý</li></ul> | <ul><li>Obor názvů</li><li>Fronta</li><li>Téma</li><li>Předplatné</li><li>Centrum událostí</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>Pravidlo</li>Klientská organizace</li> |

## <a name="autoscale-logs-schema"></a>Schéma protokolů automatického škálování
JSON protokolu automatického škálování obsahuje prvky uvedené v následující tabulce:

| Název | Description |
| ---- | ----------- | 
| `TrackingId` | Interní ID, které se používá pro účely trasování |
| `ResourceId` | ID prostředku Azure Resource Manager. |
| `Message` | Informační zpráva, která poskytuje podrobné informace o automatické neploché akci. Zpráva obsahuje předchozí a aktuální hodnotu jednotky propustnosti pro daný obor názvů a, která aktivovala neplochý počet výskytů. |

Tady je příklad události automatického škálování: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>Schéma protokolů koordinátora Kafka
JSON protokolu Kafka Coordinator obsahuje prvky uvedené v následující tabulce:

| Název | Description |
| ---- | ----------- | 
| `RequestId` | ID žádosti, která se používá pro účely trasování |
| `ResourceId` | ID prostředku Azure Resource Manager |
| `Operation` | Název operace, která se má provést během koordinace skupiny |
| `ClientId` | ID klienta |
| `NamespaceName` | Název oboru názvů | 
| `SubscriptionId` | ID předplatného Azure |
| `Message` | Informativní nebo varovné zprávy, které poskytují podrobné informace o akcích provedených během koordinace skupiny. |

### <a name="example"></a>Příklad

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Schéma protokolů chyb uživatele Kafka
JSON protokolu chyb uživatele Kafka obsahuje prvky uvedené v následující tabulce:

| Název | Description |
| ---- | ----------- |
| `TrackingId` | ID sledování, které se používá pro účely trasování. |
| `NamespaceName` | Název oboru názvů |
| `Eventhub` | Název centra událostí |
| `PartitionId` | ID oddílu |
| `GroupId` | ID skupiny |
| `ClientId` | ID klienta |
| `ResourceId` | ID prostředku Azure Resource Manager. |
| `Message` | Informační zpráva, která poskytuje podrobné informace o chybě |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs schéma událostí připojení k virtuální síti
Event Hubs JSON události připojení virtuální sítě (VNet) obsahuje prvky uvedené v následující tabulce:

| Název | Description |
| ---  | ----------- | 
| `SubscriptionId` | ID předplatného Azure |
| `NamespaceName` | Název oboru názvů |
| `IPAddress` | IP adresa klienta připojujícího se ke službě Event Hubs |
| `Action` | Akce prováděná službou Event Hubs při vyhodnocování požadavků na připojení. Podporované akce **akceptují připojení** a **zamítají připojení**. |
| `Reason` | Poskytuje důvod, proč byla akce dokončena. |
| `Count` | Počet výskytů pro danou akci |
| `ResourceId` | ID prostředku Azure Resource Manager. |

Protokoly virtuální sítě se generují jenom v případě, že obor názvů umožňuje přístup z **vybraných sítí** nebo z **konkrétních IP adres** (pravidla filtru IP). Pokud nechcete omezit přístup k vašemu oboru názvů pomocí těchto funkcí a přesto chcete získat protokoly virtuální sítě ke sledování IP adres klientů připojujících se k Event Hubsmu oboru názvů, můžete použít následující alternativní řešení. Povolte filtrování IP adres a přidejte celkový adresovatelný rozsah IPv4 (1.0.0.0/1-255.0.0.0/1). Event Hubs nepodporuje rozsahy IPv6. 

### <a name="example"></a>Příklad

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Klíčové uživatelské protokoly spravované uživatelem
Kód JSON klíče uživatele spravovaný klíčem zákazníka obsahuje prvky uvedené v následující tabulce:

| Název | Description |
| ---- | ----------- | 
| `Category` | Typ kategorie pro zprávu Je to jedna z následujících hodnot: **Chyba** a **informace** |
| `ResourceId` | ID interního prostředku, což zahrnuje ID předplatného Azure a název oboru názvů |
| `KeyVault` | Název prostředku Key Vault |
| `Key` | Název Key Vaultho klíče |
| `Version` | Verze Key Vaultho klíče |
| `Operation` | Název operace, která byla provedena k obsluze požadavků |
| `Code` | Stavový kód |
| `Message` | Zpráva, která poskytuje podrobné informace o chybě nebo informativní zprávě |



## <a name="next-steps"></a>Další kroky
- [Úvod do Event Hubs](./event-hubs-about.md)
- [Ukázky Event Hubs](sdks.md)
- Začínáme se službou Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
