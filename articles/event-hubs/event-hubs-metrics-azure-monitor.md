---
title: Metriky Azure Event Hubs ve službě Azure Monitor (preview) | Dokumentace Microsoftu
description: Monitorování služby Event Hubs pomocí monitorování Azure
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e01262d67b6d08cd1cb05a6375344a5087fffce3
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012517"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Metriky Azure Event Hubs ve službě Azure Monitor (preview)

Metriky Event Hubs poskytuje stav služby Event Hubs prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posouzení celkového stavu služby event hubs, pouze na úrovni oboru názvů, ale také na úrovni entity. Ve statistikách může být důležité, protože pomáhají s monitorováním stavu služby event hubs. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst Azure Monitor s využitím .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázka na Githubu.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď metriky přístup prostřednictvím [webu Azure portal](https://portal.azure.com), nebo pomocí rozhraní API služby Azure Monitor (REST a .NET) a řešení pro analýzu, jako je například Operations Management Suite a Služba Event Hubs. Další informace najdete v tématu [monitorování dat shromážděných službou Azure Monitor](../azure-monitor/platform/data-collection.md).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Toto je nakonfigurováno v [nastavení diagnostiky](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) ve službě Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete sledovat v čase [webu Azure portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![Zobrazit úspěšné metriky][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Uděláte to tak, zvolte svůj obor názvů a pak klikněte na tlačítko **metriky (Peview)**. Pokud chcete zobrazit metriky vyfiltrovaný tak, aby oboru centra událostí, vyberte Centrum událostí a klikněte na **metriky (preview)**.

Pro metrika podporuje dimenze musí filtr s hodnotou požadované dimenze, jak je znázorněno v následujícím příkladu:

![Filtr s hodnotu dimenze][2]

## <a name="billing"></a>Fakturace

Použití metrik ve službě Azure Monitor je aktuálně zdarma ve verzi preview. Ale pokud používáte další řešení, která ingestovat data metrik, vám může účtovat podle těchto řešení. Například jste se účtují po Azure Storage při archivaci dat metrik do účtu služby Azure Storage. Se taky účtují Azure Pokud streamování dat metrik do Log Analytics pro pokročilé analýzy.

Následujících metrik získáte přehled o stavu vaší služby. 

> [!NOTE]
> Jak se přesunout pod jiným názvem jsme se vyřazení několik metrik. To může vyžadovat aktualizaci vašeho odkazů. Metriky, které jsou označeny klíčovým slovem "zastaralé" nebude podporovat do budoucna.

Všechny hodnoty metriky se posílají do Azure monitoru každou minutu. Časové intervaly definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporované časový interval pro všechny metriky služby Event Hubs je 1 minuta.

## <a name="request-metrics"></a>Metrika žádosti

Spočítá počet dat a správu požadavků operace.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí žádosti (preview) | Počet požadavků provedených na službu Azure Event Hubs v zadaném období. <br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |
| Úspěšné požadavky (preview)   | Počet úspěšných požadavků provedených na službu Azure Event Hubs v zadaném období. <br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |
| Chyby serveru (preview) | Počet požadavků není zpracována z důvodu chyby ve službě Azure Event Hubs v zadaném období. <br/><br/>Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |
|Chyby uživatele (preview)|Počet požadavků není zpracována z důvodu chyby uživatele v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby překročení kvóty (preview)|Počet žádostí překročil dostupnou kvótu. Zobrazit [v tomto článku](event-hubs-quotas.md) Další informace o kvótách služby Event Hubs.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="throughput-metrics"></a>Metriky propustnosti

| Název metriky | Popis |
| ------------------- | ----------------- |
|Omezené požadavky (preview)|Počet požadavků, které byly omezené, protože byl překročen využití jednotek propustnosti.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy (preview)|Počet zpráv odeslaných do služby Event Hubs v zadaném období nebo události.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Odchozí zprávy (preview)|Počet zpráv nebo událostí načte ze služby Event Hubs v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Příchozí bajty (preview)|Počet bajtů odeslaných do služby Azure Event Hubs v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Odchozí bajty (preview)|Počet bajtů načtených ze služby Azure Event Hubs v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="connection-metrics"></a>Metrik připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections (preview)|Počet aktivních připojení na obor názvů i na entity.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení otevřeno (preview)|Počet otevřených připojení.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení uzavřeno (preview)|Počet uzavřených připojení.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="event-hubs-capture-metrics"></a>Metriky Event Hubs Capture

Metriky Event Hubs Capture můžete sledovat, když povolíte funkci Capture pro vaši službu event hubs. Následující metriky popisují, co můžete monitorovat pomocí zapnutým zachytáváním.

| Název metriky | Popis |
| ------------------- | ----------------- |
|Zachytit Backlog (Preview)|Počet bajtů, které ještě mají být zaznamenány do zvoleného cíle.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Zachycené zprávy (Preview)|Počet zpráv nebo událostí, které jsou zachyceny do zvoleného cíle v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Zachycené bajty (Preview)|Počet bajtů, které jsou zachyceny do zvoleného cíle v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Event Hubs podporuje následující dimenze pro metriky ve službě Azure Monitor. Přidání dimenzí k metriky je nepovinné. Pokud nepřidáte dimenze, jsou určeny metriky na úrovni oboru názvů. 

| Název metriky | Popis |
| ------------------- | ----------------- |
|EntityName| Event Hubs podporuje entit centra událostí v rámci oboru názvů.|

## <a name="next-steps"></a>Další postup

* Zobrazit [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Načíst metriky Azure Monitor s využitím .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázka na Githubu. 

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



