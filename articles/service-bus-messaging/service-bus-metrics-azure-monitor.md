---
title: Metriky Azure Service Bus ve službě Azure Monitor (preview) | Dokumentace Microsoftu
description: Monitorování entit služby Service Bus pomocí Azure Monitoring
services: service-bus-messaging
documentationcenter: .NET
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: 5175d768f4aa62365e4151b4c8fed372038f1d95
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548795"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Metriky Azure Service Bus ve službě Azure Monitor (preview)

Metrik služby Service Bus vám poskytnou stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posouzení celkového stavu vašich prostředků služby Service Bus, pouze na úrovni oboru názvů, ale také na úrovni entity. Ve statistikách může být důležité, protože pomáhají s monitorováním stavu služby Service Bus. Metriky může také pomoct potíží hlavní příčinu, aniž byste museli kontaktovat podporu Azure.

Azure Monitor nabízí jednotné uživatelské rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a [metriky načíst Azure Monitor s využitím .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) ukázka na Githubu.

> [!IMPORTANT]
> Pokud nebyl všechny interakce s entitou, další 2 hodiny, metriky se spustí zobrazující "0" jako hodnotu, dokud entita již není nečinná.

## <a name="access-metrics"></a>Metriky přístup

Azure Monitor poskytuje několik způsobů přístupu metriky. Můžete buď metriky přístup prostřednictvím [webu Azure portal](https://portal.azure.com), nebo pomocí rozhraní API služby Azure Monitor (REST a .NET) a řešení pro analýzu například Log Analytics a Event Hubs. Další informace najdete v tématu [monitorování dat shromážděných službou Azure Monitor](../azure-monitor/platform/data-collection.md).

Ve výchozím nastavení jsou povolené metriky a posledních 30 dnů dat můžete přistupovat. Pokud je potřeba data uchovávat po delší dobu, můžete archivovat data metrik do účtu služby Azure Storage. Tato hodnota je nakonfigurovaný v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) ve službě Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup metrik na portálu

Metriky můžete sledovat v čase [webu Azure portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí žádosti na úrovni účtu:

![][1]

Můžete také přístup k metrikám přímo prostřednictvím oboru názvů. Uděláte to tak, zvolte svůj obor názvů a pak klikněte na tlačítko **metriky (Preview)**. Pokud chcete zobrazit metriky vyfiltrovaný tak, aby oboru entity, vyberte entitu a pak klikněte na **metriky (preview)**.

![][2]

Pro metrika podporuje dimenze musí filtr s hodnotou požadované dimenzí.

## <a name="billing"></a>Fakturace

Použití metrik ve službě Azure Monitor je zdarma ve verzi preview. Ale pokud používáte další řešení, která ingestovat data metrik, vám může účtovat podle těchto řešení. Například jste se účtují po Azure Storage při archivaci dat metrik do účtu služby Azure Storage. Můžete také účtují se službou Log Analytics Pokud streamování dat metrik do Log Analytics pro pokročilé analýzy.

Následujících metrik získáte přehled o stavu vaší služby. 

> [!NOTE]
> Jak se přesunout pod jiným názvem jsme se vyřazení několik metrik. To může vyžadovat aktualizaci vašeho odkazů. Metriky, které jsou označeny klíčovým slovem "zastaralé" nebude podporovat do budoucna.

Všechny hodnoty metriky se posílají do Azure monitoru každou minutu. Časové intervaly definuje časový interval, pro které jsou uvedeny hodnoty metrik. Podporované časový interval pro všechny metriky služby Service Bus je 1 minuta.

## <a name="request-metrics"></a>Metrika žádosti

Spočítá počet dat a správu požadavků operace.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí žádosti (preview) | Počet požadavků provedených na službu Service Bus v zadaném období. <br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
|Úspěšné požadavky (preview)|Počet úspěšných požadavků provedených na službu Service Bus v zadaném období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
|Chyby serveru (preview)|Počet požadavků není zpracována z důvodu chyby ve službě Service Bus v zadaném období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
|Chyby uživatele (ve verzi preview – viz následující podčásti)|Počet požadavků není zpracována z důvodu chyby uživatele v zadaném období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
|Omezené požadavky (preview)|Počet požadavků, které byly omezené, protože byl překročen využití.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|

### <a name="user-errors"></a>Chyby uživatele

Následující dva typy chyb jsou klasifikovány jako chyby uživatele:

1. Chyby na straně klienta (v protokolu HTTP, která by byla 400 chyb).
2. Chyby, ke kterým dochází při zpracování zpráv, jako například [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy (preview)|Počet událostí nebo zprávy odeslané do služby Service Bus v zadaném období.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
|Odchozí zprávy (preview)|Počet zpráv přijatých z Service Bus v zadaném období nebo události.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
| Zprávy (preview) | Počet zpráv ve frontě nebo tématu. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Dimenze: EntityName |
| ActiveMessages (preview) | Počet aktivních zpráv ve frontě nebo tématu. <br/><br/> Jednotka: Počet <br/> Typ agregace: Průměr <br/> Dimenze: EntityName |

## <a name="connection-metrics"></a>Metrik připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections (preview)|Počet aktivních připojení na obor názvů i na entity.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
|Připojení otevřeno (preview)|Počet otevřených připojení.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName|
|Připojení uzavřeno (preview)|Počet uzavřených připojení.<br/><br/> Jednotka: Počet <br/> Typ agregace: Celkem <br/> Dimenze: EntityName |

## <a name="resource-usage-metrics"></a>Metriky využití prostředků

> [!NOTE] 
> Tyto metriky jsou k dispozici pouze **premium** vrstvy. 

| Název metriky | Popis |
| ------------------- | ----------------- |
|Využití CPU na obor názvů (preview)|Procento využití procesoru oboru názvů.<br/><br/> Jednotka: Procento <br/> Typ agregace: Maximum <br/> Dimenze: EntityName|
|Využití paměti na obor názvů (preview)|Procento využití paměti oboru názvů.<br/><br/> Jednotka: Procento <br/> Typ agregace: Maximum <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metriky

Azure Service Bus podporuje následující dimenze pro metriky ve službě Azure Monitor. Přidání dimenzí k metriky je nepovinné. Pokud nepřidáte dimenze, jsou určeny metriky na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|EntityName| Service Bus podporuje entit pro zasílání zpráv v rámci oboru názvů.|

## <a name="set-up-alerts-on-metrics"></a>Nastavte si výstrahy týkající se metrik

1. Na **metriky** karty **služby Service Bus Namespace** stránce **konfigurace výstrah**. 

    ![Metriky stránce – Konfigurace upozornění nabídky](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Vyberte **Výběr cíle**, a proveďte následující akce na **vyberte prostředek** stránky: 
    1. Vyberte **obory názvů služby Service Bus** pro **filtrovat podle typu prostředku** pole. 
    2. Vyberte své předplatné pro **filtrovat podle předplatného** pole.
    3. Vyberte **obor názvů služby Service bus** ze seznamu. 
    4. Vyberte **Done** (Hotovo). 
    
        ![Výběr oboru názvů](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Vyberte **přidat kritéria**, a proveďte následující akce na **konfigurovat logiku signálů** stránky:
    1. Vyberte **metriky** pro **signalizuje, že typ**. 
    2. Vyberte signál. Příklad: **Chyby (Preview) služby**. 

        ![Vyberte chyby serveru](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Vyberte **větší než** pro **podmínku**.
    2. Vyberte **celkový** pro **Časová agregace**. 
    3. Zadejte **5** pro **prahová hodnota**. 
    4. Vyberte **Done** (Hotovo).    

        ![Zadejte podmínku](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na **vytvořit pravidlo** stránce, rozbalte **definujte podrobnosti o upozornění**, a proveďte následující akce:
    1. Zadejte **název** výstrahy. 
    2. Zadejte **popis** výstrahy.
    3. Vyberte **závažnost** výstrahy. 

        ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na **vytvořit pravidlo** stránce, rozbalte **definujte skupinu akcí**vyberte **nová skupina akcí**, a proveďte následující akce na **stránku skupiny přidat akci**. 
    1. Zadejte název skupiny akcí.
    2. Zadejte krátký název skupiny akcí. 
    3. Vyberte své předplatné. 
    4. Vyberte skupinu prostředků. 
    5. V tomto návodu, zadejte **odeslání e-mailu** pro **název akce**.
    6. Vyberte **e-mailu/SMS nebo nabízená/hlasové** pro **typ akce**. 
    7. Vyberte **upravit podrobnosti**. 
    8. Na **e-mailu/SMS nebo nabízená/hlasové** stránce, proveďte následující akce:
        1. Vyberte **e-mailu**. 
        2. Typ **e-mailová adresa**. 
        3. Vyberte **OK**.

            ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na **přidat skupinu akcí** stránce **OK**. 
1. Na **vytvořit pravidlo** stránce **vytvořit pravidlo upozornění**. 

    ![Vytvoření pravidla upozornění tlačítka](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Další postup

Zobrazit [Přehled monitorování Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


