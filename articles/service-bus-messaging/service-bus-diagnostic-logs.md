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
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261807"
---
# <a name="service-bus-diagnostic-logs"></a>Protokoly diagnostiky Service Bus

Pro Azure Service Bus můžete zobrazit dva typy protokolů:
* **[Protokoly aktivit](../azure-monitor/platform/activity-logs-overview.md)** . Tyto protokoly obsahují informace o operacích provedených v rámci úlohy. Protokoly jsou vždy povoleny.
* **[Diagnostické protokoly](../azure-monitor/platform/resource-logs-overview.md)** . Můžete nakonfigurovat diagnostické protokoly pro rozsáhlejší informace o všeho, co se v rámci úlohy děje. Diagnostické protokoly aktivit titulní od okamžiku vytvoření úlohy, až do odstranění úlohy, včetně aktualizací a aktivity, ke kterým dochází, když úloha běží.

## <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly

Diagnostické protokoly jsou ve výchozím nastavení zakázané. Chcete-li povolit diagnostické protokoly, proveďte následující kroky:

1.  V [webu Azure portal](https://portal.azure.com)v části **monitorování a správa**, klikněte na tlačítko **diagnostické protokoly**.

    ![navigační okno s diagnostickými protokoly](./media/service-bus-diagnostic-logs/image1.png)

2. Klikněte na prostředek, který chcete monitorovat.  

3.  Klikněte na **Zapnout diagnostiku**.

    ![Zapnout diagnostické protokoly](./media/service-bus-diagnostic-logs/image2.png)

4.  Pro **stav**, klikněte na tlačítko **na**.

    ![změnit diagnostické protokoly stavu](./media/service-bus-diagnostic-logs/image3.png)

5.  Nastavte cíl archivu, který chcete; například účet úložiště, centrum událostí nebo protokoly Azure Monitor.

6.  Uložte nové nastavení diagnostiky.

Nové nastavení se projeví během 10 minut. Pak se protokoly objeví v nakonfigurovaném cíli archivace v okně **protokoly diagnostiky** .

Další informace o konfiguraci diagnostiky, najdete v článku [přehled diagnostické protokoly Azure](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Diagnostické protokoly schématu

Všechny protokoly se ukládají ve formátu JavaScript Object Notation (JSON). Každá položka má pole řetězce, která používají formát popsaný v následující části.

## <a name="operational-logs-schema"></a>Schéma provozní protokoly

Protokoly v kategorii **OperationalLogs** zachytí, co se děje během Service Busch operací. Konkrétně tyto protokoly zachytí typ operace, včetně vytvoření fronty, použitých prostředků a stavu operace.

Řetězce JSON operační protokol obsahovat prvky uvedené v následující tabulce:

Název | Popis
------- | -------
ID aktivity | Interní ID, které se používá ke sledování
EventName | Název operace           
resourceId | ID prostředku Azure Resource Manager
SubscriptionId | ID předplatného
EventTimeString | Čas operace
EventProperties | Vlastnosti operace
Stav | Stav operace
Caller | Volající operace (Azure Portal nebo klient pro správu)
category | OperationalLogs

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

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus najdete na následujících odkazech:

* [Úvod do Service Bus](service-bus-messaging-overview.md)
* [Začínáme s Service Bus](service-bus-dotnet-get-started-with-queues.md)
