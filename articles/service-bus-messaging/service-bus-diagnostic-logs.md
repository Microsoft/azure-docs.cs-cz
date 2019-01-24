---
title: Diagnostické protokoly Azure Service Bus | Dokumentace Microsoftu
description: Zjistěte, jak nastavili odesílání diagnostických protokolů pro služby Service Bus v Azure.
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
ms.openlocfilehash: 6ac36fed8f9d3220e6caf6eba94a62d1ec8372c7
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846655"
---
# <a name="service-bus-diagnostic-logs"></a>Diagnostické protokoly služby Service Bus

Dva typy protokolů můžete zobrazit pro Azure Service Bus:
* **[Protokoly aktivit](../azure-monitor/platform/activity-logs-overview.md)**. Tyto protokoly obsahují informace o operace provedené na úlohu. Protokoly jsou vždy povoleny.
* **[Diagnostické protokoly](../azure-monitor/platform/diagnostic-logs-overview.md)**. Můžete nakonfigurovat diagnostické protokoly pro rozsáhlejší informace o všech novinkách, ke které dochází v rámci úlohy. Diagnostické protokoly aktivit titulní od okamžiku vytvoření úlohy, až do odstranění úlohy, včetně aktualizací a aktivity, ke kterým dochází, když úloha běží.

## <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly

Diagnostické protokoly jsou ve výchozím nastavení zakázané. Povolení diagnostických protokolů, proveďte následující kroky:

1.  V [webu Azure portal](https://portal.azure.com)v části **monitorování a správa**, klikněte na tlačítko **diagnostické protokoly**.

    ![navigace v okně diagnostické protokoly](./media/service-bus-diagnostic-logs/image1.png)

2. Klikněte na prostředek, který chcete monitorovat.  

3.  Klikněte na **Zapnout diagnostiku**.

    ![Zapnout diagnostické protokoly](./media/service-bus-diagnostic-logs/image2.png)

4.  Pro **stav**, klikněte na tlačítko **na**.

    ![změnit stav diagnostické protokoly](./media/service-bus-diagnostic-logs/image3.png)

5.  Nastavení cíle archiv, který chcete zjistit. například účet úložiště, Centrum událostí nebo Azure Log Analytics.

6.  Uložte nové nastavení diagnostiky.

Nové nastavení se projeví během 10 minut. Potom protokolů se objeví v nakonfigurovaných archivace cílové na **diagnostické protokoly** okno.

Další informace o konfiguraci diagnostiky, najdete v článku [přehled diagnostické protokoly Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Diagnostické protokoly schématu

Všechny protokoly se ukládají ve formátu JavaScript Object Notation (JSON). Každý záznam obsahuje pole řetězců, které používají formát je popsáno v následující části.

## <a name="operational-logs-schema"></a>Schéma provozní protokoly

Přihlásí **OperationalLogs** kategorie zaznamenat, co se stane během operace služby Service Bus. Tyto protokoly konkrétně zachytit typ operace, včetně vytvoření fronty, prostředky a stav operace.

Řetězce JSON operační protokol obsahovat prvky uvedené v následující tabulce:

Název | Popis
------- | -------
ID aktivity | Interní ID pro sledování
EventName | Název operace           
resourceId | ID prostředku Azure Resource Manageru
SubscriptionId | ID předplatného
EventTimeString | Čas operace
EventProperties | Vlastnosti operace
Status | Stav operace
Volající | Volající operace (Azure portal nebo správy klienta)
category | OperationalLogs

Tady je příklad operační protokol řetězec formátu JSON:

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

## <a name="next-steps"></a>Další postup

V následujících tématech se dozvíte více o Service Bus:

* [Úvod do služby Service Bus](service-bus-messaging-overview.md)
* [Začínáme se službou Service Bus](service-bus-dotnet-get-started-with-queues.md)
