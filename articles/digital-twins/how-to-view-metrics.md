---
title: Zobrazit metriky pomocí Azure Monitor
titleSuffix: Azure Digital Twins
description: Podívejte se, jak zobrazit metriky digitálních vláken Azure v Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 09e32d5baa367b76c34ebca28adfdd20385e4e18
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374553"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>Zobrazení a pochopení metrik digitálních vláken Azure

Tyto metriky poskytují informace o stavu prostředků digitálních vláken Azure ve vašem předplatném Azure. Metriky digitálních vláken Azure vám pomůžou vyhodnotit celkový stav služby digitálních vláken Azure a prostředků, které jsou k ní připojené. Tyto údaje zaměřené na uživatele vám pomůžou zjistit, co se týká vašich digitálních vláken Azure, a pomáhat s analýzou příčin potíží, aniž byste museli kontaktovat podporu Azure.

Metriky jsou ve výchozím nastavení povolené. Z [Azure Portal](https://portal.azure.com)můžete zobrazit metriky digitálních vláken Azure.

## <a name="how-to-view-azure-digital-twins-metrics"></a>Jak zobrazit metriky digitálních vláken Azure

1. Vytvořte instanci digitálních vláken Azure. Pokyny, jak nastavit instanci digitálních vláken Azure v tématu, najdete v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-scripted.md).

2. Najděte instanci digitálních vláken Azure v [Azure Portal](https:/portal.azure.com) (můžete pro ni otevřít stránku zadáním jejího názvu do panelu hledání na portálu). 

    V nabídce instance vyberte **metriky**.
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Snímek obrazovky zobrazující stránku s metrikami pro digitální vlákna Azure":::

    Tato stránka zobrazuje metriky pro instanci digitálních vláken Azure. Můžete také vytvořit vlastní zobrazení metriky tak, že vyberete ty, které chcete zobrazit v seznamu.
    
3. Data metriky můžete odeslat do Event Hubsho koncového bodu nebo účtu Azure Storage kliknutím na **nastavení diagnostiky** v nabídce a pak na **Přidat nastavení diagnostiky**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

    Další informace o tomto procesu najdete v tématu [*Postup: nastavení diagnostiky*](how-to-set-up-diagnostics.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Metriky digitálních vláken Azure a jejich použití

Digitální vlákna Azure nabízí několik metrik, které vám poskytnou přehled o stavu vaší instance a přidružených prostředků. Můžete také kombinovat informace z různých metrik a vykreslit větší přehled o stavu vaší instance. 

Následující tabulky popisují metriky sledované každou instancí digitálních vláken Azure a o tom, jak každá metrika souvisí s celkovým stavem vaší instance.

#### <a name="api-request-metrics"></a>Metriky žádostí API

Metriky, které se musí dělat s požadavky rozhraní API:

| Metrika | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Požadavky rozhraní API (Preview) | Count | Celkem | Počet požadavků rozhraní API provedených pro digitální vlákna čtení, zápisu, odstranění a dotazování. |  Authentication </br>Operace </br>Protokol </br>Stavový kód </br>Třída stavového kódu </br>Stavový text |
| ApiRequestsLatency | Latence požadavků rozhraní API (Preview) | Milisekund | Průměr | Doba odezvy pro žádosti rozhraní API. Tato operace odkazuje na čas od okamžiku, kdy je požadavek přijat pomocí digitálních vláken Azure, dokud služba neodešle výsledek úspěch/selhání pro digitální vlákna čtení, zápisu, odstranění a dotazování. | Authentication </br>Operace </br>Protokol |
| ApiRequestsFailureRate | Frekvence neúspěšných požadavků rozhraní API (Preview) | Procento | Průměr | Procento požadavků rozhraní API, které služba obdrží pro vaši instanci, která poskytuje kód odpovědi interní chyby (500) pro digitální vlákna čtení, zápisu, odstranění a dotazování. | Authentication </br>Operace </br>Protokol </br>Stavový kód </br>Třída stavového kódu </br>Stavový text

#### <a name="routing-metrics"></a>Metriky směrování

Metriky, které se musí dělat s směrováním:

| Metrika | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| Směrování | Směrování (Preview) | Count | Celkem | Počet zpráv směrovaných do služby Azure Endpoint koncových bodů, jako je například centrum událostí, Service Bus nebo Event Grid. | Operace </br>Výsledek |
| RoutingLatency | Latence směrování (Preview) | Milisekund | Průměr | Uplynulý čas mezi událostí, která je směrována z digitálních vláken Azure do služby Azure Endpoint Services, jako je například centrum událostí, Service Bus nebo Event Grid. | Operace </br>Výsledek |
| RoutingFailureRate | Frekvence chyb směrování (Preview) | Procento | Průměr | Procento událostí, kvůli kterým dojde k chybě při směrování z digitálních vláken Azure do služby Azure Endpoint Services, jako je například centrum událostí, Service Bus nebo Event Grid. | Operace </br>Výsledek |

#### <a name="billing-metrics"></a>Metriky fakturace

Metriky, které je potřeba provést s fakturací:

>[!NOTE]
> Ve verzi Preview **je fakturace nulové**. I když se tyto metriky stále zobrazují v seznamu s možnostmi výběru, nepoužijí se ve verzi Preview a zůstanou na nule, dokud se služba nepřesune mimo verzi Preview.

| Metrika | Zobrazovaný název metriky | Jednotka | Typ agregace| Popis | Dimenze |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Fakturační operace rozhraní API (Preview) | Count | Celkem | Metrika fakturace pro počet všech požadavků rozhraní API provedených vůči službě Azure Digital requests. | `MeterId` |
| BillingQueryUnits | Fakturační jednotky dotazů (Preview) | Count | Celkem | Počet jednotek dotazu, interně vypočtené míry využití prostředků služby, spotřebované pro provádění dotazů. K dispozici je také pomocné rozhraní API pro měření jednotek dotazu: [Třída QueryChargeHelper](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessed | Zpracované účtovací zprávy (Preview) | Count | Celkem | Metrika fakturace počtu zpráv odeslaných z digitálních vláken Azure do externích koncových bodů. | `MeterId` |

## <a name="dimensions"></a>Dimenze

Dimenze vám pomůžou identifikovat další podrobnosti o metrikách. Některé metriky směrování poskytují informace pro každý koncový bod. Následující tabulka uvádí možné hodnoty pro tyto dimenze.

| Dimenze | Hodnoty |
| --- | --- |
| Authentication | OAuth |
| Operace (pro požadavky rozhraní API) | Microsoft. DigitalTwins/DigitalTwins/DELETE</br>Microsoft. DigitalTwins/DigitalTwins/Write</br>Microsoft. DigitalTwins/DigitalTwins/Read </br>Microsoft. DigitalTwins/eventroutes/Read </br>Microsoft. DigitalTwins/eventroutes/Write </br>Microsoft. DigitalTwins/eventroutes/DELETE </br>Microsoft. DigitalTwins/modely/číst </br>Microsoft. DigitalTwins/modely/Write </br>Microsoft. DigitalTwins/modely/DELETE </br>Microsoft. DigitalTwins/dotaz/akce |
Operace (pro směrování) | Event Grid </br>Centrum událostí </br>Service Bus |
| Protokol | HTTPS |
| Výsledek | Success </br>Selhání |
| Stavový kód | 200, 404, 500 a tak dále. |
| Třída stavového kódu | 2xx, 4xx, 5xx a tak dále. |
| Stavový text | Došlo k vnitřní chybě serveru, Nenalezeno a tak dále. |

## <a name="next-steps"></a>Další kroky

Další informace o správě zaznamenaných metrik pro digitální vlákna Azure najdete v tématu [*Postup: nastavení diagnostiky*](how-to-set-up-diagnostics.md).

Nebo teď, když jste se seznámili s přehledem metrik digitálních vláken Azure, najdete na následujících odkazech, kde najdete další informace o správě digitálních vláken Azure:

* [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)
* [*Postupy: Správa vlastních modelů*](how-to-manage-model.md)
* [*Postupy: Správa digitálních vláken*](how-to-manage-twin.md)
* [*Postupy: Správa dvojitých grafů s relacemi*](how-to-manage-graph.md)
* [*Postupy: Správa koncových bodů a tras*](how-to-manage-routes.md)