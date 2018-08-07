---
title: Metriky Azure Service Bus ve službě Azure Monitor (preview) | Dokumentace Microsoftu
description: Monitorování entit služby Service Bus pomocí Azure Monitoring
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/31/2018
ms.author: sethm
ms.openlocfilehash: d27cb7a870a1a03e89ea35aa3ebe3a777483cf67
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579455"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Metriky Azure Service Bus ve službě Azure Monitor (preview)

Metrik služby Service Bus poskytuje stavu prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posouzení celkového stavu vašich prostředků služby Service Bus, pouze na úrovni oboru názvů, ale také na úrovni entity. Ve statistikách může být důležité, protože pomáhají s monitorováním stavu služby Service Bus. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst Azure Monitor s využitím .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázka na Githubu.

> [!IMPORTANT]
> Pokud nebyl všechny interakce s entitou, další 2 hodiny, metriky se spustí zobrazující "0" jako hodnotu, dokud entita již není nečinná.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď metriky přístup prostřednictvím [webu Azure portal](https://portal.azure.com), nebo pomocí rozhraní API služby Azure Monitor (REST a .NET) a řešení pro analýzu například Log Analytics a Event Hubs. Další informace najdete v tématu [metrik Azure monitoru](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Toto je nakonfigurováno v [nastavení diagnostiky](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) ve službě Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete sledovat v čase [webu Azure portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Uděláte to tak, zvolte svůj obor názvů a pak klikněte na tlačítko **metriky (Peview)**. Pokud chcete zobrazit metriky vyfiltrovaný tak, aby oboru entity, vyberte entitu a pak klikněte na **metriky (preview)**.

![][2]

Pro metrika podporuje dimenze musí filtr s hodnotou požadované dimenzí.

## <a name="billing"></a>Fakturace

Použití metrik ve službě Azure Monitor je zdarma ve verzi preview. Ale pokud používáte další řešení, která ingestovat data metrik, vám může účtovat podle těchto řešení. Například jste se účtují po Azure Storage při archivaci dat metrik do účtu služby Azure Storage. Můžete také účtují se službou Log Analytics Pokud streamování dat metrik do Log Analytics pro pokročilé analýzy.

Následujících metrik získáte přehled o stavu vaší služby. 

> [!NOTE]
> Jak se přesunout pod jiným názvem jsme se vyřazení několik metrik. To může vyžadovat aktualizaci vašeho odkazů. Metriky, které jsou označeny klíčovým slovem "zastaralé" nebude podporovat do budoucna.

Všechny hodnoty metriky se posílají do Azure monitoru každou minutu. Časové intervaly definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporované časový interval pro všechny metriky služby Service Bus je 1 minuta.

## <a name="request-metrics"></a>Metrika žádosti

Spočítá počet dat a správu požadavků operace.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí žádosti (preview) | Počet požadavků provedených na službu Service Bus v zadaném období. <br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Úspěšné požadavky (preview)|Počet úspěšných požadavků provedených na službu Service Bus v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby serveru (preview)|Počet požadavků není zpracována z důvodu chyby ve službě Service Bus v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby uživatele (ve verzi preview – viz následující podčásti)|Počet požadavků není zpracována z důvodu chyby uživatele v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Omezené požadavky (preview)|Počet požadavků, které byly omezené, protože byl překročen využití.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

### <a name="user-errors"></a>Chyby uživatele

Následující dva typy chyb jsou klasifikovány jako chyby uživatele:

1. Chyby na straně klienta (v protokolu HTTP, která by byla 400 chyb).
2. Chyby, ke kterým dochází při zpracování zpráv, jako například [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy (preview)|Počet událostí nebo zprávy odeslané do služby Service Bus v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Odchozí zprávy (preview)|Počet zpráv přijatých z Service Bus v zadaném období nebo události.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="connection-metrics"></a>Metrik připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections (preview)|Počet aktivních připojení na obor názvů i na entity.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení otevřeno (preview)|Počet otevřených připojení.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení uzavřeno (preview)|Počet uzavřených připojení.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |

## <a name="resource-usage-metrics"></a>Metriky využití prostředků

| Název metriky | Popis |
| ------------------- | ----------------- |
|Využití CPU na obor názvů (preview)|Procento využití procesoru oboru názvů.<br/><br/> Jednotka: % <br/> Typ agregace: maximální <br/> Dimenze: EntityName|
|Využití paměti na obor názvů (preview)|Procento využití paměti oboru názvů.<br/><br/> Jednotka: % <br/> Typ agregace: maximální <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Service Bus podporuje následující dimenze pro metriky ve službě Azure Monitor. Přidání dimenzí k metriky je nepovinné. Pokud nepřidáte dimenze, jsou určeny metriky na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|EntityName| Service Bus podporuje entit pro zasílání zpráv v rámci oboru názvů.|

## <a name="next-steps"></a>Další postup

Zobrazit [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


