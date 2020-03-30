---
title: Metriky Azure Service Bus ve službě Azure Monitor| Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak pomocí Azure Monitor usledovat entity Service Bus (fronty, témata a odběry).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 227dfaff211eb60c5c2b25b5c76ecc82b6ce3edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240788"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Metriky Azure Service Bus ve službě Azure Monitor

Metriky Service Bus poskytují stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrikmůžete posoudit celkový stav prostředků service bus, a to nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky mohou být důležité, protože vám pomohou sledovat stav service bus. Metriky můžou taky pomoct řešit problémy s hlavní příčinou, aniž by bylo nutné kontaktovat podporu Azure.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete [v tématu monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a načtení metrik y Azure Monitor s ukázkou rozhraní [.NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) na GitHubu.

> [!IMPORTANT]
> Pokud nedošlo k žádné interakci s entitou po dobu 2 hodin, metriky se začnou zobrazovat "0" jako hodnotu, dokud entita již není nečinná.

## <a name="access-metrics"></a>Metriky přístupu

Azure Monitor poskytuje několik způsobů, jak získat přístup k metrikám. Můžete buď přístup metriky prostřednictvím [portálu Azure](https://portal.azure.com), nebo použít Azure Monitor ROZHRANÍ API (REST a .NET) a řešení analýzy, jako jsou protokoly Azure Monitor a centra událostí. Další informace najdete [v tématu Metriky v Azure Monitoru](../azure-monitor/platform/data-platform-metrics.md).

Metriky jsou ve výchozím nastavení povolené a máte přístup k nejnovějším 30 denním datům. Pokud potřebujete uchovávat data po delší dobu, můžete archivovat data metriky do účtu Azure Storage. Tato hodnota se nakonfiguruje v [diagnostických nastaveních](../azure-monitor/platform/diagnostic-settings.md) v Azure Monitoru.

## <a name="access-metrics-in-the-portal"></a>Přístup k metrikám na portálu

Metriky v průběhu času můžete sledovat na [webu Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![][1]

K metrikám můžete také přistupovat přímo prostřednictvím oboru názvů. Chcete-li tak učinit, vyberte obor názvů a klepněte na tlačítko **Metriky**. Chcete-li zobrazit metriky filtrované podle oboru entity, vyberte entitu a klepněte na tlačítko **Metriky**.

![][2]

U metrik podporujících dimenze je nutné filtrovat s požadovanou hodnotou dimenze.

## <a name="billing"></a>Fakturace

Metriky a výstrahy na Azure Monitor se účtují na základě výstrahy. Tyto poplatky by měly být k dispozici na portálu při nastavení výstrahy a před uložením. 

Další řešení, která ingestují data metrik, jsou účtována přímo těmito řešeními. Azure Storage vám například účtuje, když archivujete data metrik do účtu Azure Storage. Služba Log Analytics vám také účtuje, pokud streamujete data metrik do služby Log Analytics pro pokročilou analýzu.

Následující metriky poskytují přehled o stavu vaší služby. 

> [!NOTE]
> Jsme zavržení několik metrik, jak jsou přesunuty pod jiným názvem. To může vyžadovat aktualizaci referencí. Metriky označené klíčovým slovem "zastaralé" nebudou do budoucna podporovány.

Všechny hodnoty metrik se do Azure Monitoru posílají každou minutu. Čas rozlišovací schopnost definuje časový interval, pro který jsou zobrazeny hodnoty metriky. Podporovaný časový interval pro všechny metriky service bus je 1 minuta.

## <a name="request-metrics"></a>Metriky požadavků

Spočítá počet požadavků na operace správy dat a správy.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí požadavky| Počet požadavků na službu Service Bus za zadané období. <br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Úspěšné požadavky|Počet úspěšných požadavků na službu Service Bus za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Chyby serveru|Počet požadavků, které nebyly zpracovány z důvodu chyby ve službě Service Bus za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Chyby uživatelů (viz následující podsekce)|Počet požadavků, které nebyly zpracovány z důvodu uživatelských chyb za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Omezené požadavky|Počet požadavků, které byly omezeny, protože bylo překročeno využití.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

### <a name="user-errors"></a>Uživatelské chyby

Následující dva typy chyb jsou klasifikovány jako uživatelské chyby:

1. Chyby na straně klienta (V PROTOKOLU HTTP by to bylo 400 chyb).
2. Chyby, ke kterým dochází při zpracování zpráv, například [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy|Počet událostí nebo zpráv odeslaných do služby Service Bus za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
|Odchozí zprávy|Počet událostí nebo zpráv přijatých ze služby Service Bus za zadané období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|
| Zprávy| Počet zpráv ve frontě nebo tématu. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Dimenze: Název entity |
| Aktivní zprávy| Počet aktivních zpráv ve frontě nebo tématu. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Dimenze: Název entity |
| Zprávy s mrtvými písmeny| Počet nedoručených zpráv ve frontě nebo tématu. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/>Dimenze: Název entity |
| Naplánované zprávy| Počet naplánovaných zpráv ve frontě nebo tématu. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr  <br/> Dimenze: Název entity |

> [!NOTE]
> Hodnoty pro následující metriky jsou hodnoty v čase. Příchozí zprávy, které byly spotřebovány bezprostředně po tomto bodu v čase nemusí být zohledněny v těchto metrik. 
> - Zprávy
> - Aktivní zprávy 
> - Zprávy s mrtvými písmeny 
> - Naplánované zprávy 

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|Aktivní připojení|Počet aktivních připojení v oboru názvů i v entitě.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: Název entity|

## <a name="resource-usage-metrics"></a>Metriky využití prostředků

> [!NOTE] 
> Následující metriky jsou k dispozici pouze s **úrovní premium.** 
> 
> Důležité metriky pro sledování případných výpadků pro obor názvů úrovně premium jsou: **využití procesoru podle oboru názvů** a velikost paměti podle oboru **názvů**. [Nastavte výstrahy](../azure-monitor/platform/alerts-metric.md) pro tyto metriky pomocí Azure Monitoru.
> 
> Další metrika, kterou můžete sledovat, je: **omezené požadavky**. Nemělo by to být problém, i když tak dlouho, dokud obor názvů zůstane v paměti, procesoru a zprostředkované připojení limity. Další informace najdete v tématu [Omezení na úrovni Azure Service Bus Premium](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Název metriky | Popis |
| ------------------- | ----------------- |
|Využití procesoru na obor názvů|Procento využití procesoru oboru názvů.<br/><br/> Jednotka: Procento <br/> Typ agregace: Maximum <br/> Dimenze: Název entity|
|Využití velikosti paměti na obor názvů|Procento využití paměti oboru názvů.<br/><br/> Jednotka: Procento <br/> Typ agregace: Maximum <br/> Dimenze: Název entity|

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Service Bus podporuje následující dimenze pro metriky v Azure Monitoru. Přidání dimenzí k metrikám je volitelné. Pokud nepřidáte dimenze, metriky jsou určeny na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|Název entity| Service Bus podporuje entity zasílání zpráv v oboru názvů.|

## <a name="set-up-alerts-on-metrics"></a>Nastavení upozornění na metriky

1. Na kartě **Metriky** na stránce Obor názvů **služby Service Bus** vyberte **Konfigurovat výstrahy**. 

    ![Stránka Metriky – nabídka Konfigurovat výstrahy](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Vyberte volbu **Vybrat cíl** a proveďte na stránce **Vybrat zdroj** následující akce: 
    1. Vyberte **obory názvů servisní sběrnice** pro pole **Filtr podle typu prostředku.** 
    2. Vyberte předplatné pro pole **Filtr podle předplatného.**
    3. Ze seznamu vyberte **obor názvů servisní sběrnice.** 
    4. Vyberte **Done** (Hotovo). 
    
        ![Výběr oboru názvů](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Vyberte **Přidat kritéria**a proveďte na stránce **Konfigurovat logiku signálu** následující akce:
    1. Vyberte **metriky** pro **typ signálu**. 
    2. Vyberte signál. Například: **Chyby služby**. 

        ![Vybrat chyby serveru](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Vyberte **možnost Větší než** pro **podmínku**.
    2. Vyberte **součet** pro **agregaci času**. 
    3. Zadejte **hodnotu 5** pro **prahovou hodnotu**. 
    4. Vyberte **Done** (Hotovo).    

        ![Určit podmínku](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na stránce **Vytvořit pravidlo** rozbalte **položku Definovat podrobnosti výstrahy**a proveďte následující akce:
    1. Zadejte **název** výstrahy. 
    2. Zadejte **popis** výstrahy.
    3. Vyberte **závažnost** výstrahy. 

        ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na stránce **Vytvořit pravidlo** rozbalte položku **Definovat skupinu akcí**, vyberte Novou **skupinu akcí**a proveďte následující akce na stránce Přidat **skupinu akcí**. 
    1. Zadejte název skupiny akcí.
    2. Zadejte krátký název skupiny akcí. 
    3. Vyberte své předplatné. 
    4. Vyberte skupinu prostředků. 
    5. V tomto návodu zadejte **odeslat e-mail** pro **název akce**.
    6. Vyberte **možnost E-mail/SMS/Push/Hlas** pro **TYP AKCE**. 
    7. Vyberte **Upravit podrobnosti**. 
    8. Na stránce **E-mail/SMS/Push/Voice** proveďte následující akce:
        1. Vyberte **Možnost E-mail**. 
        2. Zadejte **e-mailovou adresu**. 
        3. Vyberte **OK**.

            ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na stránce **Přidat skupinu akcí** vyberte **OK**. 
1. Na stránce **Vytvořit pravidlo** vyberte Vytvořit **pravidlo výstrahy**. 

    ![Tlačítko Vytvořit pravidlo výstrahy](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na [přehled Azure Monitoru](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


