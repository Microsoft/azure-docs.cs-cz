---
title: Protokoly diagnostiky Azure Service Bus | Microsoft Docs
description: Přečtěte si, jak nastavit diagnostické protokoly pro Service Bus v Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592404"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Povolit protokoly diagnostiky pro Service Bus

Když začnete používat obor názvů Azure Service Bus, možná budete chtít sledovat, jak a kdy se Váš obor názvů vytvoří, odstranit nebo otevřít. Tento článek poskytuje přehled všech dostupných provozních a diagnostických protokolů.

Azure Service Bus aktuálně podporuje protokoly aktivit a provozu, které zachytí **operace správy** , které se provádí v oboru názvů Azure Service Bus. Konkrétně tyto protokoly zachytí typ operace, včetně vytvoření fronty, použitých prostředků a stavu operace.

## <a name="operational-logs-schema"></a>Schéma provozních protokolů

Všechny protokoly se ukládají ve formátu JavaScript Object Notation (JSON) v následujících dvou umístěních.

- **AzureActivity** – zobrazí protokoly z operací nebo akcí prováděných s vaším oborem názvů na portálu nebo prostřednictvím nasazení šablon Azure Resource Manager.
- **AzureDiagnostics** – zobrazí protokoly z operací/akcí prováděných s vaším oborem názvů pomocí rozhraní API nebo prostřednictvím klientů pro správu v jazykové sadě SDK.

Řetězce JSON provozního protokolu obsahují prvky uvedené v následující tabulce:

| Name (Název) | Popis |
| ------- | ------- |
| ActivityId | Interní ID, které slouží k identifikaci zadané aktivity |
| eventName | Název operace |
| ResourceId | ID prostředku Azure Resource Manager |
| SubscriptionId | ID předplatného |
| EventTimeString | Čas operace |
| EventProperties | Vlastnosti operace |
| Stav | Stav operace |
| Volající | Volající operace (Azure Portal nebo klient pro správu) |
| Kategorie | OperationalLogs |

Tady je příklad řetězce JSON provozního protokolu:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>Jaké události/operace jsou zachyceny v provozních protokolech?

Protokoly operací zaznamenávají všechny operace správy provedené v oboru názvů Azure Service Bus. Datové operace nejsou zachyceny z důvodu vysokého objemu operací s daty, které jsou prováděny Azure Service Bus.

> [!NOTE]
> Pro lepší sledování datových operací doporučujeme použít trasování na straně klienta.

Následující operace správy jsou zachyceny v provozních protokolech – 

| Rozsah | Operace|
|-------| -------- |
| hosting | <ul> <li> Vytvořit obor názvů</li> <li> Aktualizovat obor názvů </li> <li> Odstranit obor názvů </li>  </ul> | 
| Fronta | <ul> <li> Vytvořit frontu</li> <li> Fronta aktualizací</li> <li> Odstranit frontu </li> </ul> | 
| Téma | <ul> <li> Vytvořit téma </li> <li> Aktualizovat téma </li> <li> Odstranit téma </li> </ul> |
| Předplatné | <ul> <li> Vytvoření předplatného </li> <li> Aktualizovat předplatné </li> <li> Odstranit předplatné </li> </ul> |

> [!NOTE]
> V současné době nejsou operace **čtení** sledovány v provozních protokolech.

## <a name="how-to-enable-operational-logs"></a>Jak povolit provozní protokoly?

Provozní protokoly jsou ve výchozím nastavení zakázané. Chcete-li povolit diagnostické protokoly, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com)přejděte do oboru názvů Azure Service Bus a v části **monitorování**klikněte na **nastavení diagnostiky**.

   ![navigační okno s diagnostickými protokoly](./media/service-bus-diagnostic-logs/image1.png)

2. Kliknutím na **Přidat nastavení diagnostiky** nakonfigurujte nastavení diagnostiky.  

   ![zapnout diagnostické protokoly](./media/service-bus-diagnostic-logs/image2.png)

3. Konfigurace nastavení diagnostiky
   1. Zadejte **název** pro identifikaci nastavení diagnostiky.
   2. Vyberte cíl diagnostiky.
      - Když vyberete **účet úložiště**, budete muset nakonfigurovat účet úložiště, ve kterém se bude Diagnostika ukládat.
      - Pokud vyberete možnost **centra událostí**, je nutné nakonfigurovat příslušné centrum událostí, do kterého budou zasílat streamovaná nastavení diagnostiky.
      - Pokud vyberete **Log Analytics**, je nutné určit, která instance Log Analytics bude Diagnostika odeslána.
    3. Podívejte se na **OperationalLogs**.

       ![změnit diagnostické protokoly stavu](./media/service-bus-diagnostic-logs/image3.png)

4. Klikněte na **Uložit**.


Nové nastavení se projeví přibližně po dobu 10 minut. Pak se protokoly objeví v nakonfigurovaném cíli archivace v okně **protokoly diagnostiky** .

Další informace o konfiguraci diagnostiky najdete v tématu [Přehled diagnostických protokolů Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus najdete na následujících odkazech:

* [Úvod do Service Bus](service-bus-messaging-overview.md)
* [Začínáme s Service Bus](service-bus-dotnet-get-started-with-queues.md)
