---
title: Zobrazit metriky pomocí Azure Monitor
titleSuffix: Azure Digital Twins
description: Podívejte se, jak zobrazit metriky digitálních vláken Azure v Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9b092c3c7382c984e8555125820c7c34d91f5e87
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048925"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Řešení potíží se službou Azure Digital autovlákna: metriky

Metriky popsané v tomto článku poskytují informace o stavu prostředků digitálních vláken Azure ve vašem předplatném Azure. Metriky digitálních vláken Azure vám pomůžou vyhodnotit celkový stav služby digitálních vláken Azure a prostředků, které jsou k ní připojené. Tyto údaje zaměřené na uživatele vám pomůžou zjistit, co se týká vašich digitálních vláken Azure, a pomáhat s analýzou příčin potíží, aniž byste museli kontaktovat podporu Azure.

Metriky jsou ve výchozím nastavení povolené. Z [Azure Portal](https://portal.azure.com)můžete zobrazit metriky digitálních vláken Azure.

## <a name="how-to-view-azure-digital-twins-metrics"></a>Jak zobrazit metriky digitálních vláken Azure

1. Vytvořte instanci digitálních vláken Azure. Pokyny, jak nastavit instanci digitálních vláken Azure v tématu, najdete v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md).

2. Najděte instanci digitálních vláken Azure v [Azure Portal](https://portal.azure.com) (můžete pro ni otevřít stránku zadáním jejího názvu do panelu hledání na portálu). 

    V nabídce instance vyberte **metriky**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Snímek obrazovky zobrazující stránku s metrikami pro digitální vlákna Azure":::

    Tato stránka zobrazuje metriky pro instanci digitálních vláken Azure. Můžete také vytvořit vlastní zobrazení metriky tak, že vyberete ty, které chcete zobrazit v seznamu.
    
3. Data metriky můžete odeslat do Event Hubsho koncového bodu nebo účet Azure Storage tak, že v nabídce vyberete **nastavení diagnostiky** a pak **přidáte nastavení diagnostiky**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

    Další informace o tomto procesu najdete v tématu [*řešení potíží: nastavení diagnostiky*](troubleshoot-diagnostics.md).

4. Výstrahy pro data metriky si můžete nastavit tak, že v nabídce vyberete **výstrahy** a pak na **+ nové pravidlo výstrahy**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Snímek obrazovky se stránkou a tlačítkem pro upozornění, které se má přidat":::

    Další informace o tomto procesu najdete v tématu [*řešení potíží: nastavení výstrah*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Metriky digitálních vláken Azure a jejich použití

Digitální vlákna Azure nabízí několik metrik, které vám poskytnou přehled o stavu vaší instance a přidružených prostředků. Můžete také kombinovat informace z různých metrik a vykreslit větší přehled o stavu vaší instance. 

Následující tabulky popisují metriky sledované každou instancí digitálních vláken Azure a o tom, jak každá metrika souvisí s celkovým stavem vaší instance.

#### <a name="metrics-for-tracking-service-limits"></a>Metriky pro omezení služby sledování

Tyto metriky můžete nakonfigurovat tak, aby se mohly sledovat při přístupu k [limitu publikované služby](reference-service-limits.md#functional-limits) pro určitý aspekt řešení. 

Pokud ho chcete nastavit, použijte funkci [výstrahy](troubleshoot-alerts.md) v Azure monitor. Můžete definovat prahové hodnoty pro tyto metriky, abyste zobrazili upozornění, když metrika dosáhne určitého procenta jeho publikovaného limitu.

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Počet zdvojených (Náhled) | Počet | Celkem | Celkový počet vláken v instanci digitálních vláken Azure Pomocí této metriky můžete určit, jestli se k [limitu služby](reference-service-limits.md#functional-limits) přiblížíte, aby se dosáhlo maximálního počtu vláken povolených na jednu instanci. |  Žádné |
| ModelCount | Počet modelů (Preview) | Počet | Celkem | Celkový počet modelů v instanci digitálních vláken Azure Pomocí této metriky můžete určit, jestli se blížíte [limitu služby](reference-service-limits.md#functional-limits) pro maximální počet modelů povolených na jednu instanci. | Žádné |

#### <a name="api-request-metrics"></a>Metriky žádostí API

Metriky, které se musí dělat s požadavky rozhraní API:

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Žádosti rozhraní API | Počet | Celkem | Počet požadavků rozhraní API provedených pro digitální vlákna čtení, zápisu, odstranění a dotazování. |  Přihlašovací <br>NázevOperace <br>Protokol <br>Stavový kód, <br>Třída stavového kódu, <br>Stavový text |
| ApiRequestsFailureRate | Frekvence neúspěšných požadavků rozhraní API | Procento | Průměr | Procento požadavků rozhraní API, které služba obdrží pro vaši instanci, která poskytuje kód odpovědi interní chyby (500) pro digitální vlákna čtení, zápisu, odstranění a dotazování. | Přihlašovací <br>NázevOperace <br>Protokol <br>Stavový kód, <br>Třída stavového kódu, <br>Stavový text
| ApiRequestsLatency | Latence požadavků rozhraní API | Milisekund | Průměr | Doba odezvy pro žádosti rozhraní API. Tato operace odkazuje na čas od okamžiku, kdy je požadavek přijat pomocí digitálních vláken Azure, dokud služba neodešle výsledek úspěch/selhání pro digitální vlákna čtení, zápisu, odstranění a dotazování. | Přihlašovací <br>NázevOperace <br>Protokol |

#### <a name="billing-metrics"></a>Metriky fakturace

Metriky, které je potřeba provést s fakturací:

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operace s rozhraním API pro fakturaci | Počet | Celkem | Metrika fakturace pro počet všech požadavků rozhraní API provedených vůči službě Azure Digital requests. | Meter ID |
| BillingMessagesProcessed | Zpracované účtovací zprávy | Počet | Celkem | Metrika fakturace počtu zpráv odeslaných z digitálních vláken Azure do externích koncových bodů.<br><br>Aby bylo možné považovat za jednu zprávu pro účely účtování, datová část nesmí být větší než 1 KB. Datové části větší než tato se budou počítat jako další zprávy v přírůstcích po 1 KB (takže zpráva mezi 1 a 2 KB se bude počítat jako 2 zprávy, mezi 2 a 3 KB budou 3 zprávy atd.).<br>Toto omezení platí také pro odpovědi, takže volání, které vrátí 1,5 KB v těle odpovědi, se například bude účtovat jako 2 operace. | Meter ID |
| BillingQueryUnits | Jednotky dotazů fakturace | Počet | Celkem | Počet jednotek dotazu, interně vypočtené míry využití prostředků služby, spotřebované pro provádění dotazů. K dispozici je také pomocné rozhraní API pro měření jednotek dotazu: [Třída QueryChargeHelper](/dotnet/api/azure.digitaltwins.core.querychargehelper?preserve-view=true&view=azure-dotnet) | Meter ID |

Další podrobnosti o tom, jak se účtují digitální vlákna Azure, najdete v tématu [*ceny za digitální vlákna*](https://azure.microsoft.com/pricing/details/digital-twins/)Azure.

#### <a name="ingress-metrics"></a>Metriky příchozího přenosu dat

Metriky, které se musí dělat s daty příchozího přenosu dat:

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Události příchozího přenosu dat | Počet | Celkem | Počet příchozích událostí telemetrie do digitálních vláken Azure. | Výsledek |
| IngressEventsFailureRate | Frekvence neúspěšných událostí příchozího přenosu dat | Procento | Průměr | Procento příchozích událostí telemetrie, pro které služba vrací kód odpovědi vnitřní chyba (500). | Výsledek |
| IngressEventsLatency | Latence událostí příchozího přenosu dat | Milisekund | Průměr | Čas od okamžiku, kdy událost dorazí, když je připravená na výstup pomocí digitálních vláken Azure, kdy služba odešle výsledek úspěch/selhání. | Výsledek |

#### <a name="routing-metrics"></a>Metriky směrování

Metriky, které se musí dělat s směrováním:

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Směrované zprávy | Počet | Celkem | Počet zpráv směrovaných do služby Azure Endpoint koncových bodů, jako je například centrum událostí, Service Bus nebo Event Grid. | Typ koncového bodu, <br>Výsledek |
| RoutingFailureRate | Frekvence neúspěšných směrování | Procento | Průměr | Procento událostí, kvůli kterým dojde k chybě při směrování z digitálních vláken Azure do služby Azure Endpoint Services, jako je například centrum událostí, Service Bus nebo Event Grid. | Typ koncového bodu, <br>Výsledek |
| RoutingLatency | Latence směrování | Milisekund | Průměr | Uplynulý čas mezi událostí, která je směrována z digitálních vláken Azure do služby Azure Endpoint Services, jako je například centrum událostí, Service Bus nebo Event Grid. | Typ koncového bodu, <br>Výsledek |

## <a name="dimensions"></a>Dimenze

Dimenze vám pomůžou identifikovat další podrobnosti o metrikách. Některé metriky směrování poskytují informace pro každý koncový bod. Následující tabulka uvádí možné hodnoty pro tyto dimenze.

| Dimenze | Hodnoty |
| --- | --- |
| Authentication | OAuth |
| Operace (pro požadavky rozhraní API) | Microsoft. DigitalTwins/DigitalTwins/DELETE, <br>Microsoft. DigitalTwins/DigitalTwins/Write, <br>Microsoft. DigitalTwins/DigitalTwins/Read, <br>Microsoft. DigitalTwins/eventroutes/Read, <br>Microsoft. DigitalTwins/eventroutes/Write, <br>Microsoft. DigitalTwins/eventroutes/DELETE, <br>Microsoft. DigitalTwins/modely/číst, <br>Microsoft. DigitalTwins/modely/Write, <br>Microsoft. DigitalTwins/modely/DELETE, <br>Microsoft. DigitalTwins/dotaz/akce |
| Typ koncového bodu | Event Grid <br>Centrum událostí, <br>Service Bus |
| Protokol | HTTPS |
| Výsledek | Nástup <br>Selhání |
| Stavový kód | 200, 404, 500 a tak dále. |
| Třída stavového kódu | 2xx, 4xx, 5xx a tak dále. |
| Stavový text | Došlo k vnitřní chybě serveru, Nenalezeno a tak dále. |

## <a name="next-steps"></a>Další kroky

Další informace o správě zaznamenaných metrik pro digitální vlákna Azure najdete v tématu [*řešení potíží: nastavení diagnostiky*](troubleshoot-diagnostics.md).
