---
title: Azure Service Bus metriky v Azure Monitor | Microsoft Docs
description: Tento článek vysvětluje, jak pomocí Azure Monitor monitorovat Service Bus entit (fronty, témata a odběry).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 329b930c950ea7c58bdac798fce51af152aa8ff3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260966"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus metriky v Azure Monitor

Service Bus metriky poskytují stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posoudit celkový stav svých Service Busch prostředků, nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky můžou být důležité, protože vám pomohou monitorovat stav Service Bus. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a na webu GitHub [s ukázkou načítání Azure monitor metriky s rozhraním .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) Sample.

> [!IMPORTANT]
> Pokud nedošlo k žádné interakci s entitou 2 hodiny, metrika se začne zobrazovat jako hodnota 0, dokud entita nebude nečinná.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď přistupovat ke metrikám prostřednictvím [Azure Portal](https://portal.azure.com), nebo použít rozhraní API Azure monitor (Rest a .NET) a analytická řešení, jako jsou Azure monitor protokoly a Event Hubs. Další informace najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Tato hodnota je nakonfigurována v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) v Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete monitorovat v průběhu času v [Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Provedete to tak, že vyberete svůj obor názvů a kliknete na **metriky**. Chcete-li zobrazit metriky filtrované v oboru entity, vyberte entitu a klikněte na možnost **metriky**.

![][2]

Pro metriky podporující dimenze je nutné filtrovat pomocí požadované hodnoty dimenze.

## <a name="billing"></a>Fakturace

Metriky a výstrahy na Azure Monitor se účtují podle jednotlivých výstrah. Tyto poplatky by měly být k dispozici na portálu, když je nastavená výstraha a předtím, než se uloží. 

Další řešení, která ingestují data metrik, se účtují přímo pomocí těchto řešení. Například jste se účtují po Azure Storage při archivaci dat metrik do účtu služby Azure Storage. Účtuje se taky Log Analytics, pokud streamuje data metrik pro Log Analytics pro pokročilou analýzu.

Následujících metrik získáte přehled o stavu vaší služby. 

> [!NOTE]
> Jak se přesunout pod jiným názvem jsme se vyřazení několik metrik. To může vyžadovat aktualizaci vašeho odkazů. Metriky, které jsou označeny klíčovým slovem "zastaralé" nebude podporovat do budoucna.

Všechny hodnoty metriky se posílají do Azure monitoru každou minutu. Časové intervaly definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporovaný časový interval pro všechny metriky Service Bus je 1 minuta.

## <a name="request-metrics"></a>Metrika žádosti

Spočítá počet dat a správu požadavků operace.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí žádosti| Počet požadavků provedených ve službě Service Bus v zadaném období. <br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Úspěšné požadavky|Počet úspěšných požadavků provedených ve službě Service Bus v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby serveru|Počet požadavků nezpracovaných z důvodu chyby ve službě Service Bus v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Chyby uživatelů (viz následující pododdíl)|Počet požadavků není zpracována z důvodu chyby uživatele v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Omezené žádosti|Počet požadavků, které byly omezeny, protože použití bylo překročeno.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

### <a name="user-errors"></a>Chyby uživatele

Následující dva typy chyb jsou klasifikovány jako chyby uživatele:

1. Chyby na straně klienta (v HTTP, které by 400 chyby).
2. Chyby, ke kterým dochází při zpracování zpráv, například [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy|Počet událostí nebo zpráv odeslaných do Service Bus v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
|Odchozí zprávy|Počet událostí nebo zpráv přijatých z Service Bus v zadaném období.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|
| Zprávy| Počet zpráv ve frontě nebo tématu. <br/><br/> Jednotky: počet <br/> Typ agregace: průměr <br/> Dimenze: EntityName |
| ActiveMessages| Počet aktivních zpráv ve frontě nebo tématu. <br/><br/> Jednotky: počet <br/> Typ agregace: průměr <br/> Dimenze: EntityName |
| Nedoručené zprávy| Počet nedoručených zpráv ve frontě nebo tématu <br/><br/> Jednotky: počet <br/> Typ agregace: průměr <br/>Dimenze: EntityName |
| Naplánované zprávy| Počet naplánovaných zpráv ve frontě nebo tématu. <br/><br/> Jednotky: počet <br/> Typ agregace: průměr  <br/> Dimenze: EntityName |

> [!NOTE]
> Hodnoty pro následující metriky jsou hodnoty v daném časovém okamžiku. Příchozí zprávy, které byly spotřebovány ihned po tomto okamžiku, se nemusí v těchto metrikách odrazit. 
> - Zprávy
> - Aktivní zprávy 
> - Nedoručené zprávy 
> - Naplánované zprávy 

## <a name="connection-metrics"></a>Metrik připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections|Počet aktivních připojení na obor názvů i na entity.<br/><br/> Jednotky: počet <br/> Typ agregace: Celkový počet <br/> Dimenze: EntityName|

## <a name="resource-usage-metrics"></a>Metriky využití prostředků

> [!NOTE] 
> Následující metriky jsou k dispozici pouze s úrovní **Premium** . 

| Název metriky | Popis |
| ------------------- | ----------------- |
|Využití CPU na obor názvů|Procento využití procesoru oboru názvů.<br/><br/> Jednotka: procenta <br/> Typ agregace: maximum <br/> Dimenze: EntityName|
|Využití velikosti paměti na obor názvů|Procento využití paměti oboru názvů.<br/><br/> Jednotka: procenta <br/> Typ agregace: maximum <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Service Bus podporuje následující dimenze pro metriky v Azure Monitor. Přidání dimenzí k metriky je nepovinné. Pokud nepřidáte dimenze, jsou určeny metriky na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|EntityName| Service Bus podporuje entity zasílání zpráv pod oborem názvů.|

## <a name="set-up-alerts-on-metrics"></a>Nastavení upozornění na metriky

1. Na kartě **metriky** na stránce **Service Bus obor názvů** vyberte **konfigurovat výstrahy**. 

    ![Stránka metriky – konfigurace nabídky výstrahy](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Vyberte možnost **vybrat cíl** a na stránce **Vybrat prostředek** proveďte následující akce: 
    1. V poli **Filtr podle typu prostředku** vyberte **Service Bus obory názvů** . 
    2. Vyberte své předplatné pro pole **filtrovat podle předplatného** .
    3. V seznamu vyberte **obor názvů služby Service Bus** . 
    4. Vyberte **Done** (Hotovo). 
    
        ![Výběr oboru názvů](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Vyberte **Přidat kritéria**a na stránce **Konfigurovat logiku signálu** proveďte následující akce:
    1. Vyberte **metriky** pro **typ signálu**. 
    2. Vyberte signál. Například: **chyby služby**. 

        ![Vybrat chyby serveru](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Vyberte možnost **větší než** pro **podmínku**.
    2. Vyberte **celkem** pro **agregaci času**. 
    3. Jako **prahovou hodnotu**zadejte **5** . 
    4. Vyberte **Done** (Hotovo).    

        ![Zadat podmínku](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na stránce **vytvořit pravidlo** rozbalte možnost **definovat podrobnosti výstrahy**a proveďte následující akce:
    1. Zadejte **název** výstrahy. 
    2. Zadejte **Popis** výstrahy.
    3. Vyberte **závažnost** výstrahy. 

        ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na stránce **vytvořit pravidlo** rozbalte položku **definovat skupinu akcí**, vyberte možnost **Nová skupina akcí**a na **stránce Přidat skupinu akcí**proveďte následující akce. 
    1. Zadejte název skupiny akcí.
    2. Zadejte krátký název skupiny akcí. 
    3. Vyberte své předplatné. 
    4. Vyberte skupinu prostředků. 
    5. Pro tento návod zadejte pro **název akce** **Odeslat e-mail** .
    6. Pro **typ akce**vyberte **e-mail/SMS/nabízený/hlas** . 
    7. Vyberte **Upravit podrobnosti**. 
    8. Na stránce **e-mail/SMS/Push/Voice** proveďte následující akce:
        1. Vyberte **e-mail**. 
        2. Zadejte **e-mailovou adresu**. 
        3. Vyberte **OK**.

            ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na stránce **Přidat skupinu akcí** vyberte **OK**. 
1. Na stránce **vytvořit pravidlo** vyberte **vytvořit pravidlo výstrahy**. 

    ![Tlačítko vytvořit pravidlo upozornění](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na [přehled Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


