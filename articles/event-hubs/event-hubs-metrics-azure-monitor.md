---
title: Metriky ve službě Azure Monitor – Centra událostí Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak pomocí Azure Monitoring ke sledování Azure Event Hubs
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
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 96c346f4359740fda5638dfdbe5735c5bdfce8c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162646"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metriky Azure Event Hubs ve službě Azure Monitor

Metriky Event Hubs vám poskytují stav prostředků Event Hubs ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posoudit celkový stav centra událostí nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky mohou být důležité, protože vám pomohou sledovat stav centra událostí. Metriky můžou taky pomoct řešit problémy s hlavní příčinou, aniž by bylo nutné kontaktovat podporu Azure.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete [v tématu monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a načtení metrik y Azure Monitor s ukázkou rozhraní [.NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) na GitHubu.

## <a name="access-metrics"></a>Metriky přístupu

Azure Monitor poskytuje několik způsobů, jak získat přístup k metrikám. Můžete buď přístup metriky prostřednictvím [portálu Azure](https://portal.azure.com), nebo použít Azure Monitor ROZHRANÍ API (REST a .NET) a řešení analýzy, jako je log Analytics a Event Hubs. Další informace naleznete v [tématu Monitorování dat shromážděných službou Azure Monitor](../azure-monitor/platform/data-platform.md).

Metriky jsou ve výchozím nastavení povolené a máte přístup k nejnovějším 30 denním datům. Pokud potřebujete uchovávat data po delší dobu, můžete archivovat data metriky do účtu Azure Storage. To se nakonfiguruje v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) v Azure Monitoru.


## <a name="access-metrics-in-the-portal"></a>Přístup k metrikám na portálu

Metriky v průběhu času můžete sledovat na [webu Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![Zobrazit úspěšné metriky][1]

K metrikám můžete také přistupovat přímo prostřednictvím oboru názvů. Chcete-li tak učinit, vyberte obor názvů a klepněte na tlačítko **Metriky**. Pokud chcete zobrazit metriky filtrované do oboru centra událostí, vyberte centrum událostí a klikněte na **Metriky**.

U metrik podporujících dimenze je nutné filtrovat s požadovanou hodnotou dimenze, jak je znázorněno v následujícím příkladu:

![Filtr s hodnotou dimenze][2]

## <a name="billing"></a>Fakturace

Použití metrik ve Službě Azure Monitor je momentálně zdarma. Pokud však použijete další řešení, která přihlašují data metrik, mohou vám tato řešení účtovat. Azure Storage vám například účtuje, když archivujete data metrik do účtu Azure Storage. Azure se také účtuje, pokud streamujete data metrik do protokolů Azure Monitoru pro pokročilou analýzu.

Následující metriky poskytují přehled o stavu vaší služby. 

> [!NOTE]
> Jsme zavržení několik metrik, jak jsou přesunuty pod jiným názvem. To může vyžadovat aktualizaci referencí. Metriky označené klíčovým slovem "zastaralé" nebudou do budoucna podporovány.

Všechny hodnoty metrik se do Azure Monitoru posílají každou minutu. Čas rozlišovací schopnost definuje časový interval, pro který jsou zobrazeny hodnoty metriky. Podporovaný časový interval pro všechny metriky Centra událostí je 1 minuta.

## <a name="request-metrics"></a>Metriky požadavků

Spočítá počet požadavků na operace správy dat a správy.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí požadavky  | Počet požadavků na službu Azure Event Hubs za zadané období. <br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity |
| Úspěšné požadavky    | Počet úspěšných požadavků provedených na službě Azure Event Hubs za zadané období. <br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity |
| Chyby serveru  | Počet požadavků, které nebyly zpracovány z důvodu chyby ve službě Azure Event Hubs za zadané období. <br/><br/>Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity |
|Uživatelské chyby |Počet požadavků, které nebyly zpracovány z důvodu uživatelských chyb za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Chyby překročeny kvótou |Počet požadavků překročil dostupnou kvótu. Další informace o kvótách centra událostí najdete v [tomto článku.](event-hubs-quotas.md)<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="throughput-metrics"></a>Metriky propustností

| Název metriky | Popis |
| ------------------- | ----------------- |
|Omezené požadavky |Počet žádostí, které byly omezeny, protože bylo překročeno použití jednotek propustnosti.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy |Počet událostí nebo zpráv odeslaných do centra událostí za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Odchozí zprávy |Počet událostí nebo zpráv načtených z centra událostí za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Příchozí bajty |Počet bajtů odeslaných do služby Azure Event Hubs za zadané období.<br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Odchozí bajty |Počet bajtů načtených ze služby Azure Event Hubs za zadané období.<br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|Aktivní připojení |Počet aktivních připojení v oboru názvů i v entitě.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Otevřená připojení |Počet otevřených připojení.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Připojení uzavřena |Počet uzavřených připojení.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="event-hubs-capture-metrics"></a>Metriky zachycení centra událostí

Metriky zachycení centra událostí můžete sledovat, když povolíte funkci zachycení pro centra událostí. Následující metriky popisují, co můžete sledovat s povoleným zachycením.

| Název metriky | Popis |
| ------------------- | ----------------- |
|Zachytit nevyřízené položky |Počet bajtů, které ještě nebyly zachyceny do zvoleného cíle.<br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Zachycené zprávy |Počet zpráv nebo událostí, které jsou zachyceny na vybraný cíl za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Zachycené bajty |Počet bajtů, které jsou zachyceny na vybraný cíl za zadané období.<br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Event Hubs podporuje následující dimenze pro metriky v Azure Monitoru. Přidání dimenzí k metrikám je volitelné. Pokud nepřidáte dimenze, metriky jsou určeny na úrovni oboru názvů. 

| Název metriky | Popis |
| ------------------- | ----------------- |
|Název entity| Centra událostí podporuje entity centra událostí v oboru názvů.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integrace Azure Monitoru s nástroji SIEM
Směrování dat monitorování (protokoly aktivit, diagnostické protokoly atd.) do centra událostí pomocí Azure Monitoru umožňuje snadnou integraci s nástroji pro správu informací o zabezpečení a událostí (SIEM). Další informace naleznete v následujících článcích a příspěvcích na blogu:

- [Streamování dat monitorování Azure do centra událostí pro spotřebu pomocí externího nástroje](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Úvod k integraci protokolů Azure](../security/fundamentals/azure-log-integration-overview.md)
- [Použití Azure Monitor k integraci s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

Ve scénáři, kde nástroj SIEM spotřebovává data protokolu z centra událostí, pokud se nezobrazují žádné příchozí zprávy nebo příchozí zprávy, ale žádné odchozí zprávy v grafu metrik, postupujte takto:

- Pokud neexistují **žádné příchozí zprávy**, znamená to, že služba Azure Monitor není přesunutí protokoly audit/diagnostiky do centra událostí. Otevřete lístek podpory s týmem Azure Monitor v tomto scénáři. 
- Pokud existují příchozí zprávy, ale **žádné odchozí zprávy**, znamená to, že aplikace SIEM nečte zprávy. Obraťte se na poskytovatele SIEM a zjistěte, zda je konfigurace centra událostí, které tyto aplikace správné.


## <a name="next-steps"></a>Další kroky

* Podívejte se na [přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Načíst metriky Azure Monitor s ukázkou rozhraní .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) na GitHubu. 

Další informace o službě Event Hubs naleznete pod těmito odkazy:

- Úvodní kurz služby Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



