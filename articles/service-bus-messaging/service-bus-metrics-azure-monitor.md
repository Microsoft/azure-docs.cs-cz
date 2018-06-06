---
title: Azure Service Bus metriky v nástroji Sledování Azure (preview) | Microsoft Docs
description: Použití Azure monitorování ke sledování entit služby Service Bus
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/31/2018
ms.author: sethm
ms.openlocfilehash: bb0c9fcc33d6f5b54a8c2c8ad3e356a485d6ccbb
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701290"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Azure Service Bus metriky v nástroji Sledování Azure (preview)

Metriky služby Service Bus vám dává stavu prostředků ve vašem předplatném Azure. S bohatou sadu dat metrik můžete vyhodnotit celkový stav prostředků služby Service Bus, jenom na úrovni oboru názvů, ale taky na úrovni entit. Ve statistikách může být důležité, protože pomáhají monitorovat stav služby Service Bus. Metriky také může pomoci potíže hlavní příčinu, bez nutnosti požádejte podporu Azure.

Monitorování Azure poskytuje uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst monitorování Azure s .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázku na Githubu.

## <a name="access-metrics"></a>Metriky přístup

Azure monitorování poskytuje několik způsobů metriky přístup. Můžete buď metriky přístup prostřednictvím [portál Azure](https://portal.azure.com), nebo pomocí rozhraní API Správce Azure monitorování (REST a rozhraní .NET) a řešení pro analýzu například analýzy protokolů a Event Hubs. Další informace najdete v tématu [metrik Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Ve výchozím nastavení jsou povolené metriky a dostanete nejnovější 30 dní od data. Pokud potřebujete zachování dat pro delší časové období, můžete archivovat metriky dat do účtu Azure Storage. Toto je nakonfigurováno v [nastavení pro diagnostiku](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) v Azure monitorování.

## <a name="access-metrics-in-the-portal"></a>Metriky přístup na portálu

Metriky můžete sledovat v čase v [portál Azure](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![][1]

Můžete taky přejít metriky přímo prostřednictvím oboru názvů. To pokud chcete udělat, zvolte svůj obor názvů a pak klikněte na tlačítko **metriky (Peview)**. K zobrazení metriky filtruje tak, aby oboru entity, vyberte entitu a pak klikněte na **metriky (preview)**.

![][2]

Pro podporu dimenze metriky musí filtrovat s hodnotou požadované dimenze.

## <a name="billing"></a>Fakturace

Použití metriky v Azure monitorování je zdarma při ve verzi preview. Ale pokud používáte další řešení, které ingestují data metriky, může být fakturuje podle těchto řešení. Například fakturuje se podle Azure Storage archivujete metriky dat do účtu Azure Storage. Také fakturuje se podle analýzy protokolů Pokud stream metriky dat k analýze protokolů pro účely provádění pokročilých analýz.

Následující metriky vám poskytl přehled o stavu služby. 

> [!NOTE]
> Několik metriky jsme se místo začne pohybu pod jiným názvem. To může vyžadovat aktualizaci vaše odkazy. Metriky, které jsou označené jako "zastaralé" – klíčové slovo již nebude podporována do budoucna.

Všechny hodnoty metrik odešlou do Azure monitorování každou minutu. Členitost času definuje časový interval, pro které jsou uvedené hodnoty metrik. Podporované časový interval pro všechny metriky služby Service Bus je 1 minuta.

## <a name="request-metrics"></a>Žádost o metriky

Spočítá počet požadavků operations dat a správu.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí žádosti (preview) | Počet žádostí odeslaných do služby Service Bus v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Úspěšné požadavky (preview)|Počet úspěšných požadavků provedené ve službě Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby serveru (preview)|Počet požadavků nebyl zpracován z důvodu chyby ve službě Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby uživatelského (Náhled – viz následující dílčí části)|Počet požadavků nebyl zpracován z důvodu chyb uživatele v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Omezenému požadavky (preview)|Počet požadavků, které byly omezené, protože byl překročen využití.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

### <a name="user-errors"></a>Chyby uživatelského

Následující dva typy chyb jsou klasifikovány jako chyby uživatele:

1. Klientské straně chyby (v protokolu HTTP které by 400 chyby).
2. Těmto chybám dochází při zpracování zprávy, jako například [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Zpráva metriky

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy (preview)|Počet událostí nebo zprávy odeslané do služby Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Odchozí zprávy (preview)|Počet událostí nebo zprávy přijaté ze služby Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections (preview)|Počet aktivních připojení u oboru názvů, a také na entitu.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení otevřeno (preview)|Počet otevřená připojení.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení uzavřené (preview)|Počet připojení uzavřené.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |

## <a name="resource-usage-metrics"></a>Metriky využití prostředků

| Název metriky | Popis |
| ------------------- | ----------------- |
|Využití procesoru na obor názvů (preview)|Procento využití procesoru oboru názvů.<br/><br/> Jednotka: % <br/> Typ agregace: maximální <br/> Dimenze: EntityName|
|Velikost využití paměti na obor názvů (preview)|Procento využití paměti oboru názvů.<br/><br/> Jednotka: % <br/> Typ agregace: maximální <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Service Bus podporuje následující dimenze pro metriky v Azure monitorování. Přidání dimenze pro vaše metriky je nepovinné. Pokud nepřidáte dimenzí, metriky jsou zadaná na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|entityName| Service Bus podporuje entit pro zasílání zpráv v rámci oboru názvů.|

## <a name="next-steps"></a>Další postup

Najdete v článku [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


