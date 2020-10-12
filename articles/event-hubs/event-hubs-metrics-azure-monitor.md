---
title: Metriky v Azure Monitor – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak pomocí monitorování Azure monitorovat Azure Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7ad570a41fd9dfff01e3a1da6b2d309a7a8464cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88931144"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metriky Azure Event Hubs ve službě Azure Monitor

Event Hubs metriky poskytují stav prostředků Event Hubs ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posoudit celkový stav Center událostí nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky můžou být důležité, protože vám pomohou monitorovat stav Center událostí. Metriky taky můžou pomoct řešit problémy s hlavními příčinami, aniž byste museli kontaktovat podporu Azure.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../azure-monitor/overview.md) a na webu GitHub [s ukázkou načítání Azure monitor metriky s rozhraním .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) Sample.

## <a name="access-metrics"></a>Metriky přístupu

Azure Monitor poskytuje více způsobů přístupu k metrikám. Můžete buď přistupovat ke metrikám prostřednictvím [Azure Portal](https://portal.azure.com), nebo použít rozhraní API Azure monitor (Rest a .NET) a analytická řešení, jako je například Log Analytics a Event Hubs. Další informace najdete v tématu [monitorování dat shromažďovaných pomocí Azure monitor](../azure-monitor/platform/data-platform.md).

Metriky jsou ve výchozím nastavení povolené a můžete získat přístup k nejnovějším 30 dnům dat. Pokud potřebujete zachovat data po delší dobu, můžete archivovat data metrik na účet Azure Storage. Tato možnost je nakonfigurována v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) v Azure monitor.


## <a name="access-metrics-in-the-portal"></a>Přístup k metrikám na portálu

Metriky můžete monitorovat v průběhu času v [Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![Zobrazit úspěšné metriky][1]

Metriky můžete také přistupovat přímo přes obor názvů. Provedete to tak, že vyberete svůj obor názvů a kliknete na **metriky**. Pokud chcete zobrazit metriky filtrované v oboru centra událostí, vyberte centrum událostí a pak klikněte na **metriky**.

Pro metriky podporující dimenze je nutné filtrovat pomocí požadované hodnoty dimenze, jak je znázorněno v následujícím příkladu:

![Filtrovat s hodnotou dimenze][2]

## <a name="billing"></a>Fakturace

Použití metrik v Azure Monitor je aktuálně zadarmo. Pokud ale použijete další řešení, která ingestují data metrik, můžou se tato řešení účtovat. Například se fakturuje Azure Storage, Pokud archivujete data metriky na účet Azure Storage. Azure se účtuje i v případě, že streamuje data metrik pro Azure Monitor protokoly pro pokročilou analýzu.

Následující metriky poskytují přehled o stavu služby. 

> [!NOTE]
> Nepoužíváme několik metrik, protože se přesunou pod jiným názvem. To může vyžadovat, abyste aktualizovali odkazy. Metriky označené klíčovým slovem "zastaralé" nebudou při přeposílání podporovány.

Všechny hodnoty metrik jsou odesílány do Azure Monitor každou minutu. Časové rozlišení definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaný časový interval pro všechny metriky Event Hubs je 1 minuta.

## <a name="request-metrics"></a>Metriky žádostí

Spočítá počet požadavků na data a operace správy.

| Název metriky | Description |
| ------------------- | ----------------- |
| Příchozí žádosti  | Počet požadavků provedených ve službě Azure Event Hubs v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName |
| Úspěšné požadavky    | Počet úspěšných požadavků provedených ve službě Azure Event Hubs v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName |
| Chyby serveru  | Počet požadavků nezpracovaných z důvodu chyby ve službě Azure Event Hubs v zadaném období. <br/><br/>Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName |
|Chyby uživatele |Počet požadavků nezpracovaných z důvodu chyb uživatele v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Chyby překročení kvóty |Počet požadavků překročil dostupnou kvótu. Další informace o kvótách Event Hubs najdete v [tomto článku](event-hubs-quotas.md) .<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="throughput-metrics"></a>Metriky propustnosti

| Název metriky | Description |
| ------------------- | ----------------- |
|Omezené žádosti |Počet žádostí, které byly omezeny, protože bylo překročeno použití jednotek propustnosti.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Description |
| ------------------- | ----------------- |
|Příchozí zprávy |Počet událostí nebo zpráv odeslaných do Event Hubs v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Odchozí zprávy |Počet událostí nebo zpráv načtených z Event Hubs v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Příchozí bajty |Počet bajtů odeslaných službě Azure Event Hubs v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Odchozí bajty |Počet bajtů načtených ze služby Azure Event Hubs v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Description |
| ------------------- | ----------------- |
|ActiveConnections |Počet aktivních připojení v oboru názvů i u entity.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Otevřená připojení |Počet otevřených připojení.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Uzavřená připojení |Počet uzavřených připojení<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="event-hubs-capture-metrics"></a>Zachytit metriky Event Hubs

Pokud povolíte funkci Capture pro centra událostí, můžete sledovat metriky Event Hubs zachycení. Následující metriky popisují, co můžete monitorovat s povoleným zachytáváním.

| Název metriky | Description |
| ------------------- | ----------------- |
|Backlog zachytávání |Počet bajtů, které se ještě mají zachytit do vybraného cíle.<br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Zachycené zprávy |Počet zpráv nebo událostí, které jsou zachyceny do vybraného cíle v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Zachycené bajty |Počet bajtů, které jsou zachyceny do vybraného cíle v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Event Hubs podporuje pro metriky v Azure Monitor následující dimenze. Přidávání dimenzí do metrik je volitelné. Pokud dimenze nepřidáte, jsou metriky zadány na úrovni oboru názvů. 

| Název metriky | Description |
| ------------------- | ----------------- |
|EntityName| Event Hubs podporuje entity centra událostí pod oborem názvů.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integrace Azure Monitor s nástroji SIEM
Směrování dat monitorování (protokoly aktivit, diagnostické protokoly atd.) do centra událostí s Azure Monitor umožňuje snadnou integraci s nástroji pro správu informací a zabezpečení událostí (SIEM). Další informace najdete v následujících článcích nebo blogových příspěvcích:

- [Streamování dat monitorování Azure do centra událostí pro využití externím nástrojem](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Úvod do Azure Log Integration](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Použití Azure Monitor k integraci s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

V případě, že nástroj SIEM spotřebovává data protokolu z centra událostí, pokud se nezobrazí žádné příchozí zprávy nebo se v grafu metriky nezobrazí příchozí zprávy, ale v grafu metriky nejsou žádné odchozí zprávy, postupujte takto:

- Pokud neexistují **žádné příchozí zprávy**, znamená to, že služba Azure monitor nepřesouvá protokoly auditu/diagnostiky do centra událostí. V tomto scénáři otevřete lístek podpory s týmem Azure Monitor. 
- Pokud se nejedná o příchozí zprávy, ale **žádné odchozí zprávy**, znamená to, že aplikace Siem nečte zprávy. Obraťte se na poskytovatele SIEM a zjistěte, jestli je konfigurace centra událostí pro tyto aplikace správná.


## <a name="next-steps"></a>Další kroky

* Podívejte se na téma [Přehled monitorování Azure](../azure-monitor/overview.md).
* [Načtěte Azure monitor metriky pomocí ukázky .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) na GitHubu. 

Další informace o službě Event Hubs naleznete pod těmito odkazy:

- Úvodní kurz služby Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
