---
title: Metriky v Azure Monitor – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje informace o tom, jak použít monitorování Azure k monitorování služby Azure Event Hubs
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
ms.openlocfilehash: 99b3b4b8d48ff04fc2ced686c01b2d4de12c6555
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742132"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metriky Azure Event Hubs v Azure Monitor

Metriky Event Hubs poskytuje stav služby Event Hubs prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posouzení celkového stavu služby event hubs, pouze na úrovni oboru názvů, ale také na úrovni entity. Ve statistikách může být důležité, protože pomáhají s monitorováním stavu služby event hubs. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst Azure Monitor s využitím .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázka na Githubu.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď přistupovat ke metrikám prostřednictvím [Azure Portal](https://portal.azure.com), nebo použít rozhraní API Azure monitor (Rest a .NET) a analytická řešení, jako je například Log Analytics a Event Hubs. Další informace najdete v tématu [monitorování dat shromážděných službou Azure Monitor](../azure-monitor/platform/data-platform.md).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Toto je nakonfigurováno v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) ve službě Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete sledovat v čase [webu Azure portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![Zobrazit úspěšné metriky][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Provedete to tak, že vyberete svůj obornázvů a kliknete na metriky. Pokud chcete zobrazit metriky filtrované v oboru centra událostí, vyberte centrum událostí a pak klikněte na metriky.

Pro metrika podporuje dimenze musí filtr s hodnotou požadované dimenze, jak je znázorněno v následujícím příkladu:

![Filtr s hodnotu dimenze][2]

## <a name="billing"></a>Fakturace

Použití metrik v Azure Monitor je aktuálně zadarmo. Ale pokud používáte další řešení, která ingestovat data metrik, vám může účtovat podle těchto řešení. Například jste se účtují po Azure Storage při archivaci dat metrik do účtu služby Azure Storage. Azure se účtuje i v případě, že streamuje data metrik pro Azure Monitor protokoly pro pokročilou analýzu.

Následujících metrik získáte přehled o stavu vaší služby. 

> [!NOTE]
> Jak se přesunout pod jiným názvem jsme se vyřazení několik metrik. To může vyžadovat aktualizaci vašeho odkazů. Metriky, které jsou označeny klíčovým slovem "zastaralé" nebude podporovat do budoucna.

Všechny hodnoty metriky se posílají do Azure monitoru každou minutu. Časové intervaly definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporované časový interval pro všechny metriky služby Event Hubs je 1 minuta.

## <a name="request-metrics"></a>Metrika žádosti

Spočítá počet dat a správu požadavků operace.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí požadavky  | Počet požadavků provedených na službu Azure Event Hubs v zadaném období. <br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName |
| Úspěšné požadavky    | Počet úspěšných požadavků provedených na službu Azure Event Hubs v zadaném období. <br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName |
| Chyby serveru  | Počet požadavků není zpracována z důvodu chyby ve službě Azure Event Hubs v zadaném období. <br/><br/>Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName |
|Chyby uživatele |Počet požadavků není zpracována z důvodu chyby uživatele v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Chyby překročení kvóty |Počet žádostí překročil dostupnou kvótu. Zobrazit [v tomto článku](event-hubs-quotas.md) Další informace o kvótách služby Event Hubs.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|

## <a name="throughput-metrics"></a>Metriky propustnosti

| Název metriky | Popis |
| ------------------- | ----------------- |
|Omezené žádosti |Počet požadavků, které byly omezené, protože byl překročen využití jednotek propustnosti.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|

## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy |Počet zpráv odeslaných do služby Event Hubs v zadaném období nebo události.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Odchozí zprávy |Počet zpráv nebo událostí načte ze služby Event Hubs v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Příchozí bajty |Počet bajtů odeslaných do služby Azure Event Hubs v zadaném období.<br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Odchozí bajty |Počet bajtů načtených ze služby Azure Event Hubs v zadaném období.<br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Globální EntityName|

## <a name="connection-metrics"></a>Metrik připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections |Počet aktivních připojení na obor názvů i na entity.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Otevřená připojení |Počet otevřených připojení.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Uzavřená připojení |Počet uzavřených připojení.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|

## <a name="event-hubs-capture-metrics"></a>Metriky Event Hubs Capture

Metriky Event Hubs Capture můžete sledovat, když povolíte funkci Capture pro vaši službu event hubs. Následující metriky popisují, co můžete monitorovat pomocí zapnutým zachytáváním.

| Název metriky | Popis |
| ------------------- | ----------------- |
|Zachytit nevyřízené položky |Počet bajtů, které ještě mají být zaznamenány do zvoleného cíle.<br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Zachycené zprávy |Počet zpráv nebo událostí, které jsou zachyceny do zvoleného cíle v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|Zachycené bajty |Počet bajtů, které jsou zachyceny do zvoleného cíle v zadaném období.<br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Globální EntityName|

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



