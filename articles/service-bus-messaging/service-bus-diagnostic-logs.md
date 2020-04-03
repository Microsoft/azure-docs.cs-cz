---
title: Protokoly diagnostiky azure sběrnice | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled všech protokolů provozní a diagnostiky, které jsou k dispozici pro Azure Service Bus.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a80fb97810fee04a4eb50c43178c168e66f29173
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618734"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Povolení protokolů diagnostiky pro službu Service Bus

Když začnete používat obor názvů Azure Service Bus, můžete chtít sledovat, jak a kdy je váš obor názvů vytvořen, odstraněn nebo přístup. Tento článek obsahuje přehled všech protokolů provozní a diagnostiky, které jsou k dispozici.

Azure Service Bus aktuálně podporuje aktivity a provozní protokoly, které zachycují *operace správy,* které se provádějí v oboru názvů Azure Service Bus. Konkrétně tyto protokoly zachycují typ operace, včetně vytvoření fronty, použité prostředky a stav operace.

## <a name="operational-logs-schema"></a>Schéma provozních protokolů

Všechny protokoly jsou uloženy ve formátu JavaScript Object Notation (JSON) v následujících dvou umístěních:

- **AzureActivity**: Zobrazuje protokoly z operací a akcí, které se provádějí proti vašemu oboru názvů na webu Azure Portal nebo prostřednictvím nasazení šablon Azure Resource Manager.
- **AzureDiagnostics**: Zobrazí protokoly z operací a akcí, které jsou prováděny proti oboru názvů pomocí rozhraní API nebo prostřednictvím klientů pro správu v jazyku SDK.

Provozní protokol JSON řetězce obsahují prvky uvedené v následující tabulce:

| Name (Název) | Popis |
| ------- | ------- |
| ActivityId | Interní ID, které slouží k identifikaci zadané aktivity |
| EventName | Název operace |
| ResourceId | ID prostředků Správce prostředků Azure |
| SubscriptionId | ID předplatného |
| EventTimeString | Provozní doba |
| Vlastnosti eventu | Vlastnosti operace |
| Status | Stav operace |
| Volající | Volající operace (portál Azure nebo klient pro správu) |
| Kategorie | Provozní logy |

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

## <a name="events-and-operations-captured-in-operational-logs"></a>Události a operace zachycené v provozních protokolech

Provozní protokoly zachycují všechny operace správy, které se provádějí v oboru názvů Azure Service Bus. Datové operace nejsou zachyceny, protože velký objem datových operací, které se provádějí na Azure Service Bus.

> [!NOTE]
> Chcete-li lépe sledovat datové operace, doporučujeme použít trasování na straně klienta.

V provozních protokolech jsou zachyceny následující operace správy: 

| Rozsah | Operace|
|-------| -------- |
| Obor názvů | <ul> <li> Vytvořit obor názvů</li> <li> Aktualizovat obor názvů </li> <li> Odstranit obor názvů </li> <li> Aktualizovat zásady sdíleného přístupu oboru názvů </li> </ul> | 
| Fronta | <ul> <li> Vytvořit frontu</li> <li> Aktualizovat frontu</li> <li> Odstranit frontu </li> <li> Automaticky odstranit frontu odstranění </li> </ul> | 
| Téma | <ul> <li> Vytvořit téma </li> <li> Téma aktualizace </li> <li> Odstranit téma </li> <li> Automaticky odstranit téma odstranění </li> </ul> |
| Předplatné | <ul> <li> Vytvoření předplatného </li> <li> Aktualizovat předplatné </li> <li> Odstranit předplatné </li> <li> Automatické odstranění předplatného </li> </ul> |

> [!NOTE]
> V současné době *operace čtení* nejsou sledovány v provozních protokolech.

## <a name="enable-operational-logs"></a>Povolit provozní protokoly

Provozní protokoly jsou ve výchozím nastavení zakázány. Chcete-li povolit protokoly diagnostiky, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do oboru názvů Azure Service Bus a potom v části **Monitorování**vyberte **Nastavení diagnostiky**.

   ![Odkaz "Nastavení diagnostiky"](./media/service-bus-diagnostic-logs/image1.png)

1. V podokně **Nastavení diagnostiky** vyberte **Přidat diagnostické nastavení**.  

   ![Odkaz Přidat diagnostické nastavení](./media/service-bus-diagnostic-logs/image2.png)

1. Nastavení diagnostiky nakonfigurujte následujícím způsobem:

   a. Do pole **Název** zadejte název nastavení diagnostiky.  

   b. Vyberte jeden z následujících tří cílů pro diagnostické protokoly:  
   - Pokud vyberete **archivovat do účtu úložiště**, budete muset nakonfigurovat účet úložiště, kde budou uloženy protokoly diagnostiky.  
   - Pokud vyberete **Stream do centra událostí**, budete muset nakonfigurovat centrum událostí, do kterého chcete datové proudy protokolů diagnostiky.
   - Pokud vyberete **Odeslat do analýzy protokolů**, musíte určit, do které instance analýzy protokolů bude diagnostika odeslána.  

   c. Zaškrtněte políčko **OperationalLogs.**

    ![Podokno Nastavení diagnostiky](./media/service-bus-diagnostic-logs/image3.png)

1. Vyberte **Uložit**.

Nové nastavení se projeví přibližně za 10 minut. Protokoly jsou zobrazeny v nakonfigurovaném cíli archivace v podokně **Protokoly diagnostiky.**

Další informace o konfiguraci nastavení diagnostiky najdete v [přehledu protokolů diagnostiky Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Další kroky

Další informace o službě Service Bus najdete v tématu:

* [Úvod do sběrnice](service-bus-messaging-overview.md)
* [Začínáme se službou Service Bus](service-bus-dotnet-get-started-with-queues.md)
