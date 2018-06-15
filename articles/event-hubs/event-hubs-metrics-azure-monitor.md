---
title: Azure Event Hubs metriky v nástroji Sledování Azure (preview) | Microsoft Docs
description: Použití Azure monitorování ke sledování služby Event Hubs
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
ms.date: 04/12/2018
ms.author: sethm
ms.openlocfilehash: 445b439ff77a88a4b7783427f5fab0e40a485542
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405136"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Azure Event Hubs metriky v nástroji Sledování Azure (preview)

Metriky Event Hubs poskytuje stav služby Event Hubs prostředky ve vašem předplatném Azure. S bohatou sadu dat metrik můžete vyhodnotit celkový stav služby event hubs, jenom na úrovni oboru názvů, ale taky na úrovni entit. Ve statistikách může být důležité, protože pomáhají monitorovat stav služby event hubs. Metriky také může pomoci potíže hlavní příčinu, bez nutnosti požádejte podporu Azure.

Monitorování Azure poskytuje uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst monitorování Azure s .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázku na Githubu.

## <a name="access-metrics"></a>Metriky přístup

Azure monitorování poskytuje několik způsobů metriky přístup. Můžete buď metriky přístup prostřednictvím [portál Azure](https://portal.azure.com), nebo pomocí rozhraní API Správce Azure monitorování (REST a rozhraní .NET) a řešení pro analýzu například operace Management Suite a Event Hubs. Další informace najdete v tématu [metrik Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Ve výchozím nastavení jsou povolené metriky a dostanete nejnovější 30 dní od data. Pokud potřebujete zachování dat pro delší časové období, můžete archivovat metriky dat do účtu Azure Storage. Toto je nakonfigurováno v [nastavení pro diagnostiku](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) v Azure monitorování.

## <a name="access-metrics-in-the-portal"></a>Metriky přístup na portálu

Metriky můžete sledovat v čase v [portál Azure](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![][1]

Můžete taky přejít metriky přímo prostřednictvím oboru názvů. To pokud chcete udělat, zvolte svůj obor názvů a pak klikněte na tlačítko **metriky (Peview)**. Pokud chcete zobrazit metriky filtruje tak, aby oboru centra událostí, vyberte centra událostí a pak klikněte na **metriky (preview)**.

Pro podporu dimenze metriky musí filtrovat hodnotou požadované dimenze, jak je znázorněno v následujícím příkladu:

![][2]

## <a name="billing"></a>Fakturace

Použití metriky v Azure monitorování není aktuálně volné při ve verzi preview. Ale pokud používáte další řešení, které ingestují data metriky, může být fakturuje podle těchto řešení. Například fakturuje se podle Azure Storage archivujete metriky dat do účtu Azure Storage. Také fakturuje se službou Azure Pokud stream metriky dat k analýze protokolů pro účely provádění pokročilých analýz.

Následující metriky vám poskytl přehled o stavu služby. 

> [!NOTE]
> Několik metriky jsme se místo začne pohybu pod jiným názvem. To může vyžadovat aktualizaci vaše odkazy. Metriky, které jsou označené jako "zastaralé" – klíčové slovo již nebude podporována do budoucna.

Všechny hodnoty metrik odešlou do Azure monitorování každou minutu. Členitost času definuje časový interval, pro které jsou uvedené hodnoty metrik. Podporované časový interval pro všechny metriky služby Event Hubs je 1 minuta.

## <a name="request-metrics"></a>Žádost o metriky

Spočítá počet požadavků operations dat a správu.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí žádosti (preview) | Počet žádostí odeslaných do služby Azure Event Hubs v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |
| Úspěšné požadavky (preview)   | Počet úspěšných požadavků provedené ve službě Azure Event Hubs v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |
| Chyby serveru (preview) | Počet požadavků nebyl zpracován z důvodu chyby ve službě Azure Event Hubs v zadaném období. <br/><br/>Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName |
|Chyby uživatelského (preview)|Počet požadavků nebyl zpracován z důvodu chyb uživatele v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Omezenému požadavky (preview)|Počet požadavků, které byly omezené, protože byl překročen využití jednotek propustnosti.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby překročena kvóta (preview)|Počet požadavků překročila kvótu pro dostupné. V tématu [v tomto článku](event-hubs-quotas.md) Další informace o službě Event Hubs kvóty.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="throughput-metrics"></a>Metriky propustnosti

| Název metriky | Popis |
| ------------------- | ----------------- |
|Omezenému požadavky (preview)|Počet požadavků, které byly omezené, protože byl překročen využití jednotek propustnosti.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="message-metrics"></a>Zpráva metriky

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy (preview)|Počet událostí nebo zprávy odeslané do centra událostí v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Odchozí zprávy (preview)|Počet událostí nebo zprávy načíst ze služby Event Hubs v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Příchozí bajty (preview)|Počet bajtů odeslaných do služby Azure Event Hubs v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Odchozí bajty (preview)|Počet bajtů získaný ze služby Azure Event Hubs v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections (preview)|Počet aktivních připojení u oboru názvů, a také na entitu.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení otevřeno (preview)|Počet otevřená připojení.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Připojení uzavřené (preview)|Počet připojení uzavřené.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="event-hubs-capture-metrics"></a>Metriky zaznamenat centra událostí

Zaznamenat centra událostí metriky můžete sledovat, když povolíte funkci zachycení pro event hubs. Následující metriky popisují, můžete monitorovat pomocí zachycení povoleno.

| Název metriky | Popis |
| ------------------- | ----------------- |
|Zaznamenat nevyřízených položek (Preview)|Počet bajtů, které ještě mají se zachytit k vybrané cílové.<br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Zachycené zprávy (Preview)|Počet zpráv nebo událostí, které jsou vybrané cílové zachycen v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Zaznamenané bajtů (Preview)|Počet bajtů, které jsou vybrané cílové zachycen v zadaném období.<br/><br/> Jednotka: bajtů <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Event Hubs podporuje následující dimenze pro metriky v Azure monitorování. Přidání dimenze pro vaše metriky je nepovinné. Pokud nepřidáte dimenzí, metriky jsou zadaná na úrovni oboru názvů. 

| Název metriky | Popis |
| ------------------- | ----------------- |
|entityName| Služba Event Hubs podporuje entit centra událostí pod oborem názvů.|

## <a name="next-steps"></a>Další postup

* Najdete v článku [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Načtení metrik Azure monitorování s .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázku na Githubu. 

Další informace o službě Event Hubs naleznete pod těmito odkazy:

* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



