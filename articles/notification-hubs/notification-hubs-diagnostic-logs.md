---
title: Protokoly diagnostiky Azure Notification Hubs | Microsoft Docs
description: Tento článek poskytuje přehled všech provozních a diagnostických protokolů, které jsou k dispozici pro Azure Notification Hubs.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b98a04a70062461cec603bea83052c4f1224819e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736232"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Povolit protokoly diagnostiky pro Notification Hubs

Když začnete používat obor názvů Azure Notification Hubs, možná budete chtít sledovat, jak a kdy se Váš obor názvů vytvoří, odstraněný nebo přistupuje. Tento článek poskytuje přehled všech dostupných provozních a diagnostických protokolů.

Služba Azure Notification Hubs aktuálně podporuje protokoly aktivit a provozních protokolů, které provádějí *operace správy* zachycení v oboru názvů Azure Notification Hubs.

## <a name="diagnostic-logs-schema"></a>Schéma diagnostických protokolů

Všechny protokoly jsou uložené ve formátu JavaScript Object Notation (JSON) v následujících dvou umístěních:

- **AzureActivity**: zobrazuje protokoly z operací a akcí, které se provádí na vašem oboru názvů ve Azure Portal nebo prostřednictvím Azure Resource Manager šablon nasazení.
- **AzureDiagnostics**: zobrazuje protokoly z operací a akcí, které se provádí v rámci vašeho oboru názvů pomocí rozhraní API, nebo prostřednictvím klientů pro správu v jazykové sadě SDK.

Řetězce JSON pro diagnostické protokoly obsahují prvky uvedené v následující tabulce:

| Název | Popis |
| ------- | ------- |
| time | Časové razítko UTC protokolu |
| resourceId | Relativní cesta k prostředku Azure |
| operationName | Název operace správy |
| category | Kategorie protokolu. Platné hodnoty: `OperationalLogs` |
| callerIdentity | Identita volajícího, který inicioval operaci správy |
| resultType | Stav operace správy Platné hodnoty: `Succeeded` nebo `Failed` |
| resultDescription | Popis operace správy |
| correlationId | ID korelace operace správy (Pokud je zadaná) |
| callerIpAddress | IP adresa volajícího. Prázdné pro volání, která pocházejí z Azure Portal |

Tady je příklad řetězce JSON provozního protokolu:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

`callerIdentity`Pole může být prázdné nebo řetězec JSON s jedním z následujících formátů.

Pro volání pocházející z Azure Portal `identity` pole je prázdné. Protokol může být koreluje s protokoly aktivit, aby bylo možné určit přihlášeného uživatele.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Pro volání prováděná pomocí Azure Resource Manager `identity` pole bude obsahovat uživatelské jméno přihlášeného uživatele.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

Pro volání Notification Hubs REST API `identity` pole bude obsahovat název zásad přístupu použitých k vygenerování tokenu SharedAccessSignature.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Události a operace zaznamenané v provozních protokolech

Provozní protokoly zaznamenávají všechny operace správy, které se provádějí v oboru názvů Azure Notification Hubs. Datové operace nejsou zachyceny kvůli velkému objemu operací s daty, které jsou prováděny na Azure Notification Hubs.

Následující operace správy jsou zachyceny v provozních protokolech: 

| Obor | Název operace | Popis operace |
| :-- | :-- | :-- |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/Action | Vypsat autorizační pravidla |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/DELETE | Odstranit autorizační pravidlo |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/klíče listkey/Action | Zobrazit seznam klíčů |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/Read | Získat autorizační pravidlo |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat klíče |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/autorizačních pravidel/Write | Vytvořit nebo aktualizovat autorizační pravidlo |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/DELETE | Odstranit obor názvů |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/číst | Získat obor názvů |
| Obor názvů | Microsoft. NotificationHubs/obory názvů/zápis | Vytvořit nebo aktualizovat obor názvů |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/Action | Vypsat autorizační pravidla |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/DELETE | Odstranit autorizační pravidlo |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/klíče listkey/Action | Zobrazit seznam klíčů |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/Read | Autorizační pravidlo pro čtení |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/regenerateKeys/Action | Znovu vygenerovat klíče |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel/Write | Vytvořit nebo aktualizovat autorizační pravidlo |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/DELETE | Odstranit centrum oznámení |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/pnsCredentials/Action | Vytvoření, aktualizace nebo získání přihlašovacích údajů PNS |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/Read | Získat centrum oznámení |
| Centrum oznámení | Microsoft. NotificationHubs/obory názvů/NotificationHubs/Write | Vytvořit nebo aktualizovat centrum oznámení |

## <a name="enable-operational-logs"></a>Povolit operační protokoly

Provozní protokoly jsou ve výchozím nastavení zakázané. Pokud chcete povolit protokoly, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)přejdete do oboru názvů Azure Notification Hubs a potom v části **monitorování** vyberte  **nastavení diagnostiky**.

   ![Odkaz "nastavení diagnostiky"](./media/notification-hubs-diagnostic-logs/image-1.png)

1. V podokně **nastavení diagnostiky** vyberte **Přidat nastavení diagnostiky**.  

   ![Odkaz Přidat nastavení diagnostiky](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Nastavení diagnostiky nakonfigurujte pomocí následujícího postupu:

   a. Do pole **název** zadejte název nastavení diagnostiky.  

   b. Vyberte jedno z následujících tří míst pro diagnostické protokoly:  
   - Pokud vyberete možnost **Odeslat do Log Analytics pracovní prostor**, je nutné určit, která instance Log Analytics bude Diagnostika odeslána.  
   - Pokud vyberete možnost **archivovat do účtu úložiště**, musíte nakonfigurovat účet úložiště, do kterého se budou protokoly diagnostiky ukládat.  
   - Pokud vyberete **Stream do centra událostí**, budete muset nakonfigurovat centrum událostí, do kterého chcete streamovat diagnostické protokoly.

   c. Zaškrtněte políčko **OperationalLogs** .

    ![Podokno nastavení diagnostiky](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Vyberte **Uložit**.

Nové nastavení se projeví přibližně po 10 minutách. Protokoly se zobrazí v konfigurovaném cíli archivace v podokně **diagnostické protokoly** .

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci nastavení diagnostiky najdete v těchto tématech:
* [Přehled protokolů diagnostiky Azure](../azure-monitor/essentials/platform-logs-overview.md)

Další informace o službě Azure Notification Hubs najdete v těchto tématech:
* [Co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md)