---
title: Azure Relay metriky v Azure Monitor (Preview) | Microsoft Docs
description: Monitorování Azure Relay pomocí monitorování Azure
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
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: 9933b7857580f29cbb6a54ebc82d4e7207456ad4
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261793"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Relay metriky v Azure Monitor (Preview)
Azure Relay metriky poskytují stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posoudit celkový stav svých prostředků přenosu, nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky můžou být důležité, protože vám pomohou monitorovat stav Azure Relay. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst Azure Monitor s využitím .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázka na Githubu.

> [!IMPORTANT]
> Tento článek se vztahuje pouze na funkci Hybrid Connections Azure Relay, nikoli na WCF Relay. 

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď metriky přístup prostřednictvím [webu Azure portal](https://portal.azure.com), nebo pomocí rozhraní API služby Azure Monitor (REST a .NET) a řešení pro analýzu, jako je například Operations Management Suite a Služba Event Hubs. Další informace najdete v tématu [monitorování dat shromážděných službou Azure Monitor](../azure-monitor/platform/data-platform.md).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Toto je nakonfigurováno v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) ve službě Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete sledovat v čase [webu Azure portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Provedete to tak, že vyberete svůj obor názvů a kliknete na **metriky (Preview)** . 

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
| ListenerConnections – úspěch (Preview) | Počet úspěšných připojení naslouchacího procesu provedených k Azure Relay v zadaném období. <br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|ListenerConnections-ClientError (Preview)|Počet chyb klienta na připojeních naslouchacího procesu v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|ListenerConnections-ServerError (Preview)|Počet chyb serveru v připojeních naslouchacího procesu v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|SenderConnections – úspěch (Preview)|Počet úspěšných připojení odesílatele provedených v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|SenderConnections-ClientError (Preview)|Počet chyb klienta v připojeních odesílatelů v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|SenderConnections-ServerError (Preview)|Počet chyb serveru v připojeních odesílatele v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|ListenerConnections-TotalRequests (Preview)|Celkový počet připojení naslouchacího procesu v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|SenderConnections-TotalRequests (Preview)|Žádosti o připojení provedené odesílateli v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|ActiveConnections (preview)|Počet aktivních připojení v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|ActiveListeners (Preview)|Počet aktivních naslouchací procesů v zadaném období.<br/><br/> Jednotce Count <br/> Typ agregace: Celkem <br/> Globální EntityName|
|ListenerDisconnects (Preview)|Počet odpojených naslouchacího procesu v zadaném období.<br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Globální EntityName|
|SenderDisconnects (Preview)|Počet odpojených odesílatelů v zadaném období.<br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Globální EntityName|

## <a name="memory-usage-metrics"></a>Metriky využití paměti

| Název metriky | Popis |
| ------------------- | ----------------- |
|BytesTransferred (Preview)|Počet bajtů přenesených v zadaném období.<br/><br/> Jednotce B <br/> Typ agregace: Celkem <br/> Globální EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Relay podporuje následující dimenze pro metriky v Azure Monitor. Přidání dimenzí k metriky je nepovinné. Pokud nepřidáte dimenze, jsou určeny metriky na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|EntityName| Azure Relay podporuje entity zasílání zpráv pod oborem názvů.|

## <a name="next-steps"></a>Další kroky

Zobrazit [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




