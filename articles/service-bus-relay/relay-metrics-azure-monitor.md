---
title: Metriky Azure Relay ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak můžete pomocí Azure Monitor umonitorovat stav Azure Relay.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273114"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Metriky Azure Relay ve službě Azure Monitor 
Metriky Azure Relay poskytují stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrikmůžete posoudit celkový stav prostředků přenosu, a to nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky mohou být důležité, protože vám pomůžou sledovat stav Azure Relay. Metriky můžou taky pomoct řešit problémy s hlavní příčinou, aniž by bylo nutné kontaktovat podporu Azure.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete [v tématu monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a načtení metrik y Azure Monitor s ukázkou rozhraní [.NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) na GitHubu.

> [!IMPORTANT]
> Tento článek se vztahuje jenom pro hybridní připojení funkce Azure Relay, nikoli wcf relé. 

## <a name="access-metrics"></a>Metriky přístupu

Azure Monitor poskytuje několik způsobů, jak získat přístup k metrikám. Můžete buď přístup metriky prostřednictvím [portálu Azure](https://portal.azure.com), nebo použít Azure Monitor ROZHRANÍ API (REST a .NET) a řešení analýzy, jako je operation management Suite a Event Hubs. Další informace naleznete v [tématu Monitorování dat shromážděných službou Azure Monitor](../azure-monitor/platform/data-platform.md).

Metriky jsou ve výchozím nastavení povolené a máte přístup k nejnovějším 30 denním datům. Pokud potřebujete uchovávat data po delší dobu, můžete archivovat data metriky do účtu Azure Storage. To se nakonfiguruje v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) v Azure Monitoru.

## <a name="access-metrics-in-the-portal"></a>Přístup k metrikám na portálu

Metriky v průběhu času můžete sledovat na [webu Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![][1]

K metrikám můžete také přistupovat přímo prostřednictvím oboru názvů. Chcete-li tak učinit, vyberte obor názvů a klepněte na tlačítko **Metriky **. 

U metrik podporujících dimenze je nutné filtrovat s požadovanou hodnotou dimenze.

## <a name="billing"></a>Fakturace

Použití metrik ve Službě Azure Monitor je momentálně zdarma ve verzi Preview. Pokud však použijete další řešení, která přihlašují data metrik, mohou vám tato řešení účtovat. Azure Storage vám například účtuje, když archivujete data metrik do účtu Azure Storage. Protokoly Azure Monitoru se také účtují, pokud streamujete data metrik do protokolů Azure Monitoru pro pokročilou analýzu.

Následující metriky poskytují přehled o stavu vaší služby. 

> [!NOTE]
> Jsme zavržení několik metrik, jak jsou přesunuty pod jiným názvem. To může vyžadovat aktualizaci referencí. Metriky označené klíčovým slovem "zastaralé" nebudou do budoucna podporovány.

Všechny hodnoty metrik se do Azure Monitoru posílají každou minutu. Čas rozlišovací schopnost definuje časový interval, pro který jsou zobrazeny hodnoty metriky. Podporovaný časový interval pro všechny metriky Azure Relay je 1 minuta.

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
| ListenerConnections-Úspěch  | Počet úspěšných naslouchacích proces připojení k Azure Relay za zadané období. <br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Chyba ListenerConnections-ClientError |Počet chyb klienta v připojení naslouchací proces za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|ListenerConnections-ServerError |Počet chyb serveru na naslouchací proces připojení za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|SenderConnections-Úspěch |Počet úspěšných připojení odesílatele provedených za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Chyba SenderConnections-ClientError |Počet chyb klienta u připojení odesílatele za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Chyba senderConnections-ServerError |Počet chyb serveru u připojení odesílatele za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|ListenerConnections-TotalRequests |Celkový počet připojení naslouchací proces za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|SenderConnections-TotalRequests |Požadavky na připojení podaná odesílateli za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Aktivní připojení |Počet aktivních připojení. Tato hodnota je hodnota bodu v čase.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Aktivní posluchači |Počet aktivních posluchačů. Tato hodnota je hodnota bodu v čase.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|ListenerDisconnects |Počet odpojených naslouchacích procesech za zadané období.<br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|OdesílatelOdpojidne |Počet odpojených odesílatelů za zadané období.<br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="memory-usage-metrics"></a>Metriky využití paměti

| Název metriky | Popis |
| ------------------- | ----------------- |
|BytesPřevedeno |Počet bajtů převedených za zadané období.<br/><br/> Jednotka: Bajty <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Relay podporuje následující dimenze pro metriky v Azure Monitoru. Přidání dimenzí k metrikám je volitelné. Pokud nepřidáte dimenze, metriky jsou určeny na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|Název entity| Azure Relay podporuje entity zasílání zpráv v rámci oboru názvů.|

## <a name="next-steps"></a>Další kroky

Podívejte se na [přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




