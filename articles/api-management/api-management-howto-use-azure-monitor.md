---
title: Kurz – monitorování publikovaných rozhraní API v Azure API Management | Microsoft Docs
description: Pomocí kroků v tomto kurzu se naučíte používat metriky, výstrahy, protokoly aktivit a protokoly prostředků ke sledování vašich rozhraní API v Azure API Management.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 031b3bfd30e0272589c5c7ee2c546b9459767b33
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183188"
---
# <a name="tutorial-monitor-published-apis"></a>Kurz: monitorování publikovaných rozhraní API

Pomocí Azure Monitor můžete vizualizovat metriky nebo protokoly přicházející ze služby Azure API Management a provádět na nich dotazy, směrovat je, archivovat je a provádět s nimi akce.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazit metriky rozhraní API 
> * Nastavení pravidla výstrahy 
> * Zobrazení protokolů aktivit
> * Povolit a zobrazit protokoly prostředků

## <a name="prerequisites"></a>Předpoklady

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Zobrazení metrik vašich rozhraní API

API Management generuje [metriky](../azure-monitor/platform/data-platform-metrics.md) každou minutu a poskytuje vám téměř v reálném čase přehled o stavu a stavu vašich rozhraní API. Níže jsou uvedené dvě nejčastěji používané metriky. Seznam všech dostupných metrik najdete v tématu [podporované metriky](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice).

* **Kapacita** – pomáhá při rozhodování o upgradu/downgrade služeb APIM Services. Metrika se generuje každou minutu a odráží kapacitu brány v čase vytvoření sestavy. Její hodnoty se pohybují v rozsahu od 0 do 100 a počítají se na základě prostředků brány, jako je využití procesoru nebo paměti.
* **Požadavky** – pomáhají analyzovat provoz rozhraní API, který prochází přes vaše API Management služby. Metrika je vygenerována za minutu a oznamuje počet požadavků brány s dimenzemi, včetně kódů odpovědí, umístění, názvu hostitele a chyb. 

> [!IMPORTANT]
> Následující metriky jsou zastaralé od května 2019 a budou vyřazeny v srpnu 2023: celkový počet požadavků brány, úspěšné požadavky brány, neautorizované žádosti o bránu, neúspěšné požadavky brány, další požadavky brány. Migrujte prosím na metriku požadavků, která poskytuje ekvivalentní funkce.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Snímek obrazovky s metrikami v API Management přehledu":::

Přístup k metrikám:

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management. Na stránce **Přehled** zkontrolujte klíčové metriky pro vaše rozhraní API.
1. Pokud chcete podrobně prozkoumat metriky, vyberte z nabídky v dolní části stránky **metriky** .

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Snímek obrazovky položky metrik v nabídce monitorování":::

1. V rozevíracím seznamu vyberte metriky, které vás zajímají. Například **požadavky**. 
1. Graf zobrazí celkový počet volání rozhraní API.
1. Graf lze filtrovat pomocí dimenzí metriky **požadavků** . Vyberte například **Přidat filtr**, vyberte **kategorie kódu odpovědi back-end** a jako hodnotu zadejte 500. Graf teď zobrazuje počet požadavků, které se v back-endu rozhraní API nezdařily.   

## <a name="set-up-an-alert-rule"></a>Nastavení pravidla výstrahy 

Můžete přijímat [výstrahy](../azure-monitor/platform/alerts-metric-overview.md) na základě metrik a protokolů aktivit. Azure Monitor umožňuje [nakonfigurovat výstrahu](../azure-monitor/platform/alerts-metric.md) , která při triggeru provede následující akce:

* Odeslání e-mailového oznámení
* Volání webhooku
* Vyvolání aplikace logiky Azure

Postup konfigurace ukázkového pravidla výstrahy na základě metriky požadavku:

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V řádku nabídek poblíž dolního okraje stránky vyberte **výstrahy** .

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Snímek obrazovky s možností upozornění v nabídce monitorování":::

1. Vyberte **+ Nové pravidlo upozornění**.
1. V okně **vytvořit pravidlo výstrahy** **Vyberte podmínka**.
1. V okně **Konfigurovat logiku signálu** :
    1. V **typ signálu** vyberte **metriky**.
    1. V **názvu signálu** vyberte **požadavky**.
    1. V **rozdělit podle dimenzí** v **názvu dimenze** vyberte **kategorie kódu odpovědi brány**.
    1. V **hodnotách dimenze** vyberte **4xx**, v případě chyb klienta, například neoprávněných nebo neplatných požadavků.
    1. V poli **logika výstrahy** zadejte prahovou hodnotu, po které má být výstraha aktivována, a vyberte možnost **Hotovo**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Snímek obrazovky s konfigurací okna pro logiku signálů":::

1. Vyberte existující skupinu akcí nebo vytvořte novou. V následujícím příkladu je vytvořena nová skupina akcí. Pošle se vám e-mail s oznámením admin@contoso.com . 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Snímek obrazovky s oznámeními pro novou skupinu akcí":::

1. Zadejte název a popis pravidla výstrahy a vyberte úroveň závažnosti. 
1. Vyberte **Vytvořit pravidlo upozornění**.
1. Nyní otestujte pravidlo výstrahy voláním rozhraní API pro konferenci bez klíče rozhraní API. Příklad:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Na základě zkušebního období se aktivuje výstraha a pošle se e-mail na adresu admin@contoso.com . 

    Výstrahy se také zobrazí na stránce s **upozorněními** pro instanci API Management.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Snímek obrazovky s upozorněními na portálu":::

## <a name="activity-logs"></a>Protokoly aktivit

Protokoly aktivit poskytují přehled o operacích provedených vašimi službami API Management. Pomocí protokolů aktivit můžete zjistit obsah, autora a čas veškerých operací zápisu (PUT, POST, DELETE) provedených ve vašich službách API Management.

> [!NOTE]
> Protokoly aktivit nezahrnují operace čtení (GET) ani operace prováděné na webu Azure Portal nebo pomocí původních rozhraní API pro správu.

Protokoly aktivit můžete zobrazit ve své službě API Management nebo k nim můžete získat přístup ze všech svých prostředků Azure prostřednictvím služby Azure Monitor. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Snímek obrazovky s protokolem aktivit na portálu":::

Postup zobrazení protokolu aktivit:

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.

1. Vyberte **Protokol aktivit**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Snímek obrazovky položky protokolu aktivit v nabídce monitorování":::
1. Vyberte požadovaný obor filtrování a pak **použít**.

## <a name="resource-logs"></a>Protokoly prostředků

Protokoly prostředků poskytují podrobné informace o operacích a chybách, které jsou důležité pro auditování, a také pro účely řešení potíží. Protokoly prostředků se liší od protokolů aktivit. Protokol aktivit nabízí přehled o operacích provedených na vašich prostředcích Azure. Protokoly prostředků poskytují přehled o operacích, které provedl váš prostředek.

Konfigurace protokolů prostředků:

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
2. Vyberte **nastavení diagnostiky**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Snímek obrazovky s položkou nastavení diagnostiky v nabídce monitorování":::

1. Vyberte **+ Přidat nastavení diagnostiky**.
1. Vyberte protokoly nebo metriky, které chcete shromáždit.

   Protokoly prostředků můžete archivovat spolu s metrikami v účtu úložiště, streamovat je do centra událostí nebo je odeslat do Log Analytics pracovního prostoru. 

Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro odesílání protokolů platforem a metrik do různých umístění](../azure-monitor/platform/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Zobrazit diagnostická data v Azure Monitor

Pokud povolíte shromažďování GatewayLogs nebo metrik v pracovním prostoru Log Analytics, může trvat několik minut, než se data zobrazí v Azure Monitor. Zobrazení dat:

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V nabídce v dolní části stránky vyberte **protokoly** .

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Snímek obrazovky s položkou Logs v nabídce monitorování":::

Spusťte dotazy pro zobrazení dat. Je k dispozici několik [ukázkových dotazů](../azure-monitor/log-query/example-queries.md) nebo vlastní spuštění. Například následující dotaz načte nejaktuálnější 24 hodin dat z tabulky GatewayLogs:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Další informace o použití protokolů prostředků pro API Management najdete v tématech:

* Začněte [s Azure Monitor Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)nebo vyzkoušejte [ukázkové prostředí Log Analytics](https://portal.loganalytics.io/demo).

* [Přehled dotazů protokolu v Azure monitor](../azure-monitor/log-query/log-query-overview.md).

Následující JSON označuje vzorový záznam v GatewayLogs pro úspěšnou žádost o rozhraní API. Podrobnosti najdete v [referenčních](gateway-log-schema-reference.md)informacích ke schématu. 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazit metriky rozhraní API
> * Nastavení pravidla výstrahy 
> * Zobrazení protokolů aktivit
> * Povolit a zobrazit protokoly prostředků


Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Trasování volání](api-management-howto-api-inspector.md)