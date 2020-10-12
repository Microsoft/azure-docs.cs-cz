---
title: Protokoly diagnostiky Azure Service Bus | Microsoft Docs
description: Tento článek poskytuje přehled všech provozních a diagnostických protokolů, které jsou k dispozici pro Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f227f5a988ccd51425b4f43e87b4ed4d9af74e1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064430"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Povolit protokoly diagnostiky pro Service Bus

Když začnete používat Azure Service Bus obor názvů, můžete chtít sledovat, jak a kdy se Váš obor názvů vytváří, odstranil nebo je k němu přistupoval. Tento článek poskytuje přehled všech dostupných provozních a diagnostických protokolů.

Azure Service Bus aktuálně podporuje aktivity a provozní protokoly, které provádějí *operace správy* zachycení na Azure Service Bus oboru názvů. Konkrétně tyto protokoly zachytí typ operace, včetně vytvoření fronty, použitých prostředků a stavu operace.

## <a name="operational-logs-schema"></a>Schéma provozních protokolů

Všechny protokoly jsou uložené ve formátu JavaScript Object Notation (JSON) v následujících dvou umístěních:

- **AzureActivity**: zobrazuje protokoly z operací a akcí, které se provádí na vašem oboru názvů ve Azure Portal nebo prostřednictvím Azure Resource Manager šablon nasazení.
- **AzureDiagnostics**: zobrazuje protokoly z operací a akcí, které se provádí v rámci vašeho oboru názvů pomocí rozhraní API, nebo prostřednictvím klientů pro správu v jazykové sadě SDK.

Řetězce JSON provozního protokolu obsahují prvky uvedené v následující tabulce:

| Název | Popis |
| ------- | ------- |
| ActivityId | Interní ID, které slouží k identifikaci zadané aktivity |
| EventName | Název operace |
| ResourceId | ID prostředku Azure Resource Manager |
| SubscriptionId | ID předplatného |
| EventTimeString | Čas operace |
| EventProperties | Vlastnosti operace |
| Status | Stav operace |
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

## <a name="events-and-operations-captured-in-operational-logs"></a>Události a operace zaznamenané v provozních protokolech

Provozní protokoly zaznamenávají všechny operace správy, které jsou prováděny v oboru názvů Azure Service Bus. Datové operace nejsou zachyceny, protože se jedná o velký objem operací s daty, které jsou prováděny Azure Service Bus.

> [!NOTE]
> Abychom vám pomohli lépe sledovat datové operace, doporučujeme použít trasování na straně klienta.

Následující operace správy jsou zachyceny v provozních protokolech: 

| Rozsah | Operace|
|-------| -------- |
| Obor názvů | <ul> <li> Vytvořit obor názvů</li> <li> Aktualizovat obor názvů </li> <li> Odstranit obor názvů </li> <li> Aktualizovat zásadu SharedAccess oboru názvů </li> </ul> | 
| Fronta | <ul> <li> Vytvořit frontu</li> <li> Fronta aktualizací</li> <li> Odstranit frontu </li> <li> Odstranit frontu pro odstranění </li> </ul> | 
| Téma | <ul> <li> Vytvořit téma </li> <li> Aktualizovat téma </li> <li> Odstranit téma </li> <li> Odstranit téma autodelete </li> </ul> |
| Předplatné | <ul> <li> Vytvoření předplatného </li> <li> Aktualizovat předplatné </li> <li> Odstranit předplatné </li> <li> Odstranit předplatné autodelete </li> </ul> |

> [!NOTE]
> V současné době nejsou operace *čtení* sledovány v provozních protokolech.

## <a name="enable-operational-logs"></a>Povolit operační protokoly

Provozní protokoly jsou ve výchozím nastavení zakázané. Pokud chcete povolit diagnostické protokoly, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)přejdete do oboru názvů Azure Service Bus a v části **monitorování**vyberte  **nastavení diagnostiky**.

   ![Odkaz "nastavení diagnostiky"](./media/service-bus-diagnostic-logs/image1.png)

1. V podokně **nastavení diagnostiky** vyberte **Přidat nastavení diagnostiky**.  

   ![Odkaz Přidat nastavení diagnostiky](./media/service-bus-diagnostic-logs/image2.png)

1. Nastavení diagnostiky nakonfigurujte pomocí následujícího postupu:

   a. Do pole **název** zadejte název nastavení diagnostiky.  

   b. Vyberte jedno z následujících tří míst pro diagnostické protokoly:  
   - Pokud vyberete možnost **archivovat do účtu úložiště**, musíte nakonfigurovat účet úložiště, do kterého se budou protokoly diagnostiky ukládat.  
   - Pokud vyberete **Stream do centra událostí**, budete muset nakonfigurovat centrum událostí, do kterého chcete streamovat diagnostické protokoly.
   - Pokud vyberete možnost **Odeslat do Log Analytics**, je nutné určit, která instance Log Analytics bude Diagnostika odeslána.  

   c. Zaškrtněte políčko **OperationalLogs** .

    ![Podokno nastavení diagnostiky](./media/service-bus-diagnostic-logs/image3.png)

1. Vyberte **Uložit**.

Nové nastavení se projeví přibližně po 10 minutách. Protokoly se zobrazí v konfigurovaném cíli archivace v podokně **diagnostické protokoly** .

Další informace o konfiguraci nastavení diagnostiky najdete v tématu [Přehled protokolů diagnostiky Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus najdete v těchto tématech:

* [Úvod do Service Bus](service-bus-messaging-overview.md)
* [Začínáme se službou Service Bus](service-bus-dotnet-get-started-with-queues.md)