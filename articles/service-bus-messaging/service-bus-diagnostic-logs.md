---
title: Diagnostické protokoly služby Azure Service Bus | Microsoft Docs
description: Zjistěte, jak pro nastavení diagnostické protokoly pro Service Bus v Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 4ce724adc9ca167634be9a0b7137b6a3d54211bf
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29122170"
---
# <a name="service-bus-diagnostic-logs"></a>Diagnostické protokoly služby Service Bus

Pro Azure Service Bus můžete zobrazit dva typy protokolů:
* **[Protokoly aktivity](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Tyto protokoly obsahují informace o operace provedené na úlohu. Protokoly jsou vždy povolena.
* **[Diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Můžete nakonfigurovat diagnostické protokoly bohatší informace o všechno, co se děje v rámci úlohy. Diagnostické protokoly titulní aktivit od okamžiku, kdy úloha je vytvořena až do odstranění úlohy, včetně aktualizací a aktivity, ke kterým dochází při běhu úlohy.

## <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly

Diagnostické protokoly jsou zakázané ve výchozím nastavení. Pokud chcete povolit diagnostické protokoly, proveďte následující kroky:

1.  V [portál Azure](https://portal.azure.com)v části **monitorování + správu**, klikněte na tlačítko **protokolů diagnostiky**.

    ![Okno navigace k diagnostickým protokolům](./media/service-bus-diagnostic-logs/image1.png)

2. Klikněte na prostředek, který chcete monitorovat.  

3.  Klikněte na tlačítko **zapněte diagnostiku**.

    ![Zapnout diagnostické protokoly](./media/service-bus-diagnostic-logs/image2.png)

4.  Pro **stav**, klikněte na tlačítko **na**.

    ![změnit stav diagnostických protokolů](./media/service-bus-diagnostic-logs/image3.png)

5.  Nastavit cíl archiv, který chcete zjistit. například účet úložiště, centra událostí nebo analýza protokolů Azure.

6.  Uložte nové nastavení diagnostiky.

Nové nastavení se projeví ve přibližně 10 minut. Potom protokolů se objeví v nakonfigurovaných archivace cíl, na **protokolů diagnostiky** okno.

Další informace o konfiguraci diagnostiky, najdete v článku [přehled Azure diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Diagnostické protokoly schématu

Všechny protokoly se ukládají ve formátu JavaScript Object Notation (JSON). Každá položka má pole řetězce, které používají formát popsaný v následující části.

## <a name="operational-logs-schema"></a>Schéma operační protokoly

Přihlásí **OperationalLogs** kategorie zachycení, co se stane, že během operace služby Service Bus. Konkrétně tyto protokoly zachytit typ operace, včetně vytváření fronty, prostředky využívané a stav operace.

Řetězce formátu JSON operační protokol obsahovat prvky uvedené v následující tabulce:

Název | Popis
------- | -------
ID aktivity | Interní ID, použité pro sledování
EventName | Název operace           
resourceId | ID prostředku Azure Resource Manager
SubscriptionId | ID předplatného
EventTimeString | Operace čas
EventProperties | Vlastnosti operace
Status | Stav operace
Volající | Volající operace (Azure portal nebo správu klienta)
category | OperationalLogs

Tady je příklad protokol provozní řetězce formátu JSON:

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

V následujících tématech se dozvíte více o sběrnice:

* [Úvod do služby Service Bus](service-bus-messaging-overview.md)
* [Začínáme se službou Service Bus](service-bus-dotnet-get-started-with-queues.md)
