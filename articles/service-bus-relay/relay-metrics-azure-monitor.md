---
title: Azure metriky předávání v programu Sledování Azure (preview) | Microsoft Docs
description: Pomocí Azure Monitoring monitorovat předávání přes Azure
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
ms.openlocfilehash: b919f1fb0799ec497742115b2611f34872af2027
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure metriky předávání v programu Sledování Azure (preview)

Azure předávání metriky vám dává stavu prostředků ve vašem předplatném Azure. S bohatou sadu dat metrik můžete vyhodnotit celkový stav předávání prostředky, ne jenom na úrovni oboru názvů, ale taky na úrovni entit. Ve statistikách může být důležité, protože pomáhají monitorovat stav předávání přes Azure. Metriky také může pomoci potíže hlavní příčinu, bez nutnosti požádejte podporu Azure.

Monitorování Azure poskytuje uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst monitorování Azure s .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázku na Githubu.

## <a name="access-metrics"></a>Metriky přístup

Azure monitorování poskytuje několik způsobů metriky přístup. Můžete buď metriky přístup prostřednictvím [portál Azure](https://portal.azure.com), nebo pomocí rozhraní API Správce Azure monitorování (REST a rozhraní .NET) a řešení pro analýzu například operace Management Suite a Event Hubs. Další informace najdete v tématu [metrik Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Ve výchozím nastavení jsou povolené metriky a dostanete nejnovější 30 dní od data. Pokud potřebujete zachování dat pro delší časové období, můžete archivovat metriky dat do účtu Azure Storage. Toto je nakonfigurováno v [nastavení pro diagnostiku](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) v Azure monitorování.

## <a name="access-metrics-in-the-portal"></a>Metriky přístup na portálu

Metriky můžete sledovat v čase v [portál Azure](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![][1]

Můžete taky přejít metriky přímo prostřednictvím oboru názvů. To pokud chcete udělat, zvolte svůj obor názvů a pak klikněte na tlačítko **metriky (Peview)**. 

Pro podporu dimenze metriky musí filtrovat s hodnotou požadované dimenze.

## <a name="billing"></a>Fakturace

Použití metriky v Azure monitorování není aktuálně volné při ve verzi preview. Ale pokud používáte další řešení, které ingestují data metriky, může být fakturuje podle těchto řešení. Například fakturuje se podle Azure Storage archivujete metriky dat do účtu Azure Storage. Také fakturuje se podle analýzy protokolů Pokud stream metriky dat k analýze protokolů pro účely provádění pokročilých analýz.

Následující metriky vám poskytl přehled o stavu služby. 

> [!NOTE]
> Několik metriky jsme se místo začne pohybu pod jiným názvem. To může vyžadovat aktualizaci vaše odkazy. Metriky, které jsou označené jako "zastaralé" – klíčové slovo již nebude podporována do budoucna.

Všechny hodnoty metrik odešlou do Azure monitorování každou minutu. Členitost času definuje časový interval, pro které jsou uvedené hodnoty metrik. Podporované časový interval pro všechny metriky předávání přes Azure je 1 minuta.

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
| ListenerConnections – úspěch (preview) | Počet úspěšně naslouchací proces připojení k předávání přes Azure v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-ClientError (preview)|Počet chyb klienta na naslouchací proces připojení v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-ServerError (preview)|Počet chyb serveru na naslouchací proces připojení v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections – úspěch (preview)|Počet úspěšně odesílatele připojení vytvořená v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-ClientError (preview)|Počet chyb klientů na připojení odesilatele v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-ServerError (preview)|Počet chyb serveru na připojení odesilatele v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerConnections-TotalRequests (preview)|Celkový počet připojení naslouchací proces v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderConnections-TotalRequests (preview)|Požadavky na připojení provedené odesílatelé v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ActiveConnections (preview)|Počet aktivních připojení v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ActiveListeners (preview)|Počet aktivních naslouchací procesy v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|ListenerDisconnects (preview)|Počet odpojené naslouchací procesy v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|SenderDisconnects (preview)|Počet odpojené odesílatelé v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="memory-usage-metrics"></a>Metriky využití paměti

| Název metriky | Popis |
| ------------------- | ----------------- |
|BytesTransferred (preview)|Počet bajtů přenesených v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Předávání přes Azure podporuje následující dimenze pro metriky v Azure monitorování. Přidání dimenze pro vaše metriky je nepovinné. Pokud nepřidáte dimenzí, metriky jsou zadaná na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|entityName| Předávání přes Azure podporuje entit pro zasílání zpráv v rámci oboru názvů.|

## <a name="next-steps"></a>Další postup

Najdete v článku [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




