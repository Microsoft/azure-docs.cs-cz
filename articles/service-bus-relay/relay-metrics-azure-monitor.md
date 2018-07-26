---
title: Metriky Azure Relay ve službě Azure Monitor (preview) | Dokumentace Microsoftu
description: Monitorování Azure Relay pomocí monitorování Azure
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 3bcea7ee15bea137fecc55f1e641b84e2e72d3ff
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247434"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Metriky Azure Relay ve službě Azure Monitor (preview)

Metriky Azure Relay nabízí stavu prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posouzení celkového stavu vašich prostředků Relay, pouze na úrovni oboru názvů, ale také na úrovni entity. Ve statistikách může být důležité, protože pomáhají s monitorováním stavu služby Azure Relay. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst Azure Monitor s využitím .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázka na Githubu.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď metriky přístup prostřednictvím [webu Azure portal](https://portal.azure.com), nebo pomocí rozhraní API služby Azure Monitor (REST a .NET) a řešení pro analýzu, jako je například Operations Management Suite a Služba Event Hubs. Další informace najdete v tématu [metrik Azure monitoru](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Toto je nakonfigurováno v [nastavení diagnostiky](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) ve službě Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete sledovat v čase [webu Azure portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Uděláte to tak, zvolte svůj obor názvů a pak klikněte na tlačítko **metriky (Peview)**. 

Pro metrika podporuje dimenze musí filtr s hodnotou požadované dimenzí.

## <a name="billing"></a>Fakturace

Použití metrik ve službě Azure Monitor je aktuálně zdarma ve verzi preview. Ale pokud používáte další řešení, která ingestovat data metrik, vám může účtovat podle těchto řešení. Například jste se účtují po Azure Storage při archivaci dat metrik do účtu služby Azure Storage. Můžete také účtují se službou Log Analytics Pokud streamování dat metrik do Log Analytics pro pokročilé analýzy.

Následujících metrik získáte přehled o stavu vaší služby. 

> [!NOTE]
> Jak se přesunout pod jiným názvem jsme se vyřazení několik metrik. To může vyžadovat aktualizaci vašeho odkazů. Metriky, které jsou označeny klíčovým slovem "zastaralé" nebude podporovat do budoucna.

Všechny hodnoty metriky se posílají do Azure monitoru každou minutu. Časové intervaly definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporované časový interval pro všechny metriky Azure Relay je 1 minuta.

## <a name="connection-metrics"></a>Metrik připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
| ListenerConnections – úspěch (preview) | Počet úspěšných naslouchací proces připojení k Azure Relay v zadaném období. <br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-ClientError (preview)|Číslo chyby klienta na naslouchací proces připojení v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-ServerError (preview)|Počet chyb serveru na naslouchací proces připojení v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections – úspěch (preview)|Počet úspěšných odesílatele připojení vytvořená v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-ClientError (preview)|Číslo chyby klienta pro připojení odesílatele v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-ServerError (preview)|Počet chyb serveru pro připojení odesílatele v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-TotalRequests (preview)|Celkový počet připojení naslouchacího procesu v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-TotalRequests (preview)|Požadavky na připojení provedené odesílatelé v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ActiveConnections (preview)|Počet aktivních připojení v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ActiveListeners (preview)|Počet aktivní naslouchací procesy v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerDisconnects (preview)|Počet odpojené naslouchacích procesů v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderDisconnects (preview)|Počet odpojené odesílatelů v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="memory-usage-metrics"></a>Metriky využití paměti

| Název metriky | Popis |
| ------------------- | ----------------- |
|BytesTransferred (preview)|Počet bajtů přenesených v zadaném období.<br/><br/> Jednotka: bajty <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Relay podporuje následující dimenze pro metriky ve službě Azure Monitor. Přidání dimenzí k metriky je nepovinné. Pokud nepřidáte dimenze, jsou určeny metriky na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|EntityName| Azure Relay podporuje entit pro zasílání zpráv v rámci oboru názvů.|

## <a name="next-steps"></a>Další postup

Zobrazit [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




