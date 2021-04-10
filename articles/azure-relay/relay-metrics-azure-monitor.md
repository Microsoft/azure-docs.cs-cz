---
title: Azure Relay metriky v Azure Monitor | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak můžete pomocí Azure Monitor monitorovat stav Azure Relay.
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7f91ac0aec46d0fc24901585156dceabb18640c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586828"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay metriky v Azure Monitor 
Azure Relay metriky poskytují stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posoudit celkový stav svých prostředků přenosu, nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky můžou být důležité, protože vám pomohou monitorovat stav Azure Relay. Metriky taky můžou pomoct řešit problémy s hlavními příčinami, aniž byste museli kontaktovat podporu Azure.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../azure-monitor/overview.md) a na webu GitHub [s ukázkou načítání Azure monitor metriky s rozhraním .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) Sample.

> [!IMPORTANT]
> Tento článek se vztahuje pouze na funkci Hybrid Connections Azure Relay, nikoli na WCF Relay. 

## <a name="access-metrics"></a>Metriky přístupu

Azure Monitor poskytuje více způsobů přístupu k metrikám. Můžete buď přistupovat ke metrikám prostřednictvím [Azure Portal](https://portal.azure.com), nebo použít rozhraní API Azure monitor (Rest a .NET) a analytická řešení, jako je Operations Management Suite a Event Hubs. Další informace najdete v tématu [monitorování dat shromažďovaných pomocí Azure monitor](../azure-monitor/data-platform.md).

Metriky jsou ve výchozím nastavení povolené a můžete získat přístup k nejnovějším 30 dnům dat. Pokud potřebujete zachovat data po delší dobu, můžete archivovat data metrik na účet Azure Storage. Tato možnost je nakonfigurována v [nastavení diagnostiky](../azure-monitor/essentials/diagnostic-settings.md) v Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup k metrikám na portálu

Metriky můžete monitorovat v průběhu času v [Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![Stránka s názvem monitor – metriky (Preview) zobrazuje spojnicový graf využití paměti za posledních 30 dní.][1]

Metriky můžete také přistupovat přímo přes obor názvů. Provedete to tak, že vyberete svůj obor názvů a kliknete na **metriky**. 

Pro metriky podporující dimenze je nutné filtrovat pomocí požadované hodnoty dimenze.

## <a name="billing"></a>Fakturace

Použití metrik v Azure Monitor je v současné době ve verzi Preview zdarma. Pokud ale použijete další řešení, která ingestují data metrik, můžou se tato řešení účtovat. Například se fakturuje Azure Storage, Pokud archivujete data metriky na účet Azure Storage. Pokud streamuje data metriky do protokolů Azure Monitor pro pokročilou analýzu, budou se vám účtovat i protokoly Azure Monitor.

Následující metriky poskytují přehled o stavu služby. 

> [!NOTE]
> Nepoužíváme několik metrik, protože se přesunou pod jiným názvem. To může vyžadovat, abyste aktualizovali odkazy. Metriky označené klíčovým slovem "zastaralé" nebudou při přeposílání podporovány.

Všechny hodnoty metrik jsou odesílány do Azure Monitor každou minutu. Časové rozlišení definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaný časový interval pro všechny metriky Azure Relay je 1 minuta.

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Description |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Počet úspěšných připojení naslouchacího procesu provedených k Azure Relay v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|ListenerConnections-ClientError |Počet chyb klienta na připojeních naslouchacího procesu v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|ListenerConnections-ServerError |Počet chyb serveru v připojeních naslouchacího procesu v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|SenderConnections-Success |Počet úspěšných připojení odesílatele provedených v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|SenderConnections-ClientError |Počet chyb klienta v připojeních odesílatelů v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|SenderConnections-ServerError |Počet chyb serveru v připojeních odesílatele v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|ListenerConnections-TotalRequests |Celkový počet připojení naslouchacího procesu v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|SenderConnections-TotalRequests |Žádosti o připojení provedené odesílateli v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|ActiveConnections |Počet aktivních připojení. Tato hodnota je hodnota k určitému bodu v čase.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|ActiveListeners |Počet aktivních naslouchací proces. Tato hodnota je hodnota k určitému bodu v čase.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|ListenerDisconnects |Počet odpojených naslouchacího procesu v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|SenderDisconnects |Počet odpojených odesílatelů v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="memory-usage-metrics"></a>Metriky využití paměti

| Název metriky | Description |
| ------------------- | ----------------- |
|BytesTransferred |Počet bajtů přenesených v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Relay podporuje následující dimenze pro metriky v Azure Monitor. Přidávání dimenzí do metrik je volitelné. Pokud dimenze nepřidáte, jsou metriky zadány na úrovni oboru názvů. 

|Název dimenze|Description|
| ------------------- | ----------------- |
|EntityName| Azure Relay podporuje entity zasílání zpráv pod oborem názvů.|

## <a name="next-steps"></a>Další kroky

Podívejte se na téma [Přehled monitorování Azure](../azure-monitor/overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png
