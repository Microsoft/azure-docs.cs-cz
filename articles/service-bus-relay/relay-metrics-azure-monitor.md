---
title: Azure Relay metriky v Azure Monitor | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak můžete pomocí Azure Monitor monitorovat stav Azure Relay.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273114"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay metriky v Azure Monitor 
Azure Relay metriky poskytují stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posoudit celkový stav svých prostředků přenosu, nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky můžou být důležité, protože vám pomohou monitorovat stav Azure Relay. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a na webu GitHub [s ukázkou načítání Azure monitor metriky s rozhraním .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) Sample.

> [!IMPORTANT]
> Tento článek se vztahuje pouze na funkci Hybrid Connections Azure Relay, nikoli na WCF Relay. 

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď přistupovat ke metrikám prostřednictvím [Azure Portal](https://portal.azure.com), nebo použít rozhraní API Azure monitor (Rest a .NET) a analytická řešení, jako je Operations Management Suite a Event Hubs. Další informace najdete v tématu [monitorování dat shromažďovaných pomocí Azure monitor](../azure-monitor/platform/data-platform.md).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Tato možnost je nakonfigurována v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) v Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete monitorovat v průběhu času v [Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Provedete to tak, že vyberete svůj obor názvů a potom kliknete na * * metriky * *. 

Pro metriky podporující dimenze je nutné filtrovat pomocí požadované hodnoty dimenze.

## <a name="billing"></a>Fakturace

Použití metrik ve službě Azure Monitor je aktuálně zdarma ve verzi preview. Ale pokud používáte další řešení, která ingestovat data metrik, vám může účtovat podle těchto řešení. Například jste se účtují po Azure Storage při archivaci dat metrik do účtu služby Azure Storage. Pokud streamuje data metriky do protokolů Azure Monitor pro pokročilou analýzu, budou se vám účtovat i protokoly Azure Monitor.

Následujících metrik získáte přehled o stavu vaší služby. 

> [!NOTE]
> Jak se přesunout pod jiným názvem jsme se vyřazení několik metrik. To může vyžadovat aktualizaci vašeho odkazů. Metriky, které jsou označeny klíčovým slovem "zastaralé" nebude podporovat do budoucna.

Všechny hodnoty metriky se posílají do Azure monitoru každou minutu. Časové intervaly definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporovaný časový interval pro všechny metriky Azure Relay je 1 minuta.

## <a name="connection-metrics"></a>Metrik připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
| ListenerConnections – úspěch  | Počet úspěšných připojení naslouchacího procesu provedených k Azure Relay v zadaném období. <br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-ClientError |Počet chyb klienta na připojeních naslouchacího procesu v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-ServerError |Počet chyb serveru v připojeních naslouchacího procesu v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections – úspěch |Počet úspěšných připojení odesílatele provedených v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections – ClientError |Počet chyb klienta v připojeních odesílatelů v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-ServerError |Počet chyb serveru v připojeních odesílatele v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-TotalRequests |Celkový počet připojení naslouchacího procesu v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-TotalRequests |Žádosti o připojení provedené odesílateli v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ActiveConnections |Počet aktivních připojení. Tato hodnota je hodnota k určitému bodu v čase.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ActiveListeners |Počet aktivních naslouchací proces. Tato hodnota je hodnota k určitému bodu v čase.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerDisconnects |Počet odpojených naslouchacího procesu v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderDisconnects |Počet odpojených odesílatelů v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="memory-usage-metrics"></a>Metriky využití paměti

| Název metriky | Popis |
| ------------------- | ----------------- |
|BytesTransferred |Počet bajtů přenesených v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Relay podporuje následující dimenze pro metriky v Azure Monitor. Přidání dimenzí k metriky je nepovinné. Pokud nepřidáte dimenze, jsou určeny metriky na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|EntityName| Azure Relay podporuje entity zasílání zpráv pod oborem názvů.|

## <a name="next-steps"></a>Další kroky

Podívejte se na téma [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




