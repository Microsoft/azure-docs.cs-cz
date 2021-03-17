---
title: Integrace Azure API Management s využitím Azure Application Insights
titleSuffix: Azure API Management
description: Naučte se protokolovat a zobrazovat události z Azure API Management v Azure Application Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564241"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Postup při integraci služby Azure API Management s Azure Application Insights

Azure API Management umožňuje snadnou integraci se službou Azure Application Insights – rozšiřitelná služba pro webové vývojáře vytvářející a spravované aplikace na různých platformách. Tato příručka vás provede jednotlivými kroky této integrace a popisuje strategie pro snížení dopadu na výkon vaší instance služby API Management.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto průvodce, musíte mít instanci služby Azure API Management. Pokud ho nemáte, dokončete nejprve [kurz](get-started-create-service-instance.md) .

## <a name="create-an-application-insights-instance"></a>Vytvoření instance Application Insights

Než budete moci použít Application Insights, musíte nejprve vytvořit instanci služby. Postup vytvoření instance pomocí Azure Portal najdete v tématu [prostředky Application Insights založené na pracovních prostorech](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Vytvoření připojení mezi Application Insights a API Management

1. V **Azure Portal** přejděte do své **instance služby Azure API Management** .
1. V nabídce vlevo vyberte **Application Insights** .
1. Klikněte na **+ Přidat**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Snímek obrazovky, který ukazuje, kde přidat nové připojení":::
1. Vyberte dříve vytvořenou instanci **Application Insights** a zadejte krátký popis.
1. Klikněte na **Vytvořit**.
1. Právě jste vytvořili protokolovací nástroj Application Insights s klíčem instrumentace. Měl by se teď zobrazit v seznamu.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Snímek obrazovky, který ukazuje, kde zobrazit nově vytvořený protokolovací nástroj Application Insights pomocí klíče instrumentace":::

> [!NOTE]
> Za scénou je ve vaší instanci API Management vytvořená entita [protokolovacího](/rest/api/apimanagement/2019-12-01/logger/createorupdate) nástroje obsahující klíč instrumentace instance Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Povolení protokolování Application Insights pro vaše rozhraní API

1. V **Azure Portal** přejděte do své **instance služby Azure API Management** .
1. V nabídce na levé straně vyberte **Rozhraní API**.
1. V tomto případě **ukázkové konferenční rozhraní API** klikněte na své rozhraní API. V případě konfigurace vyberte verzi.
1. V horním panelu přejdete na kartu **Nastavení** .
1. Přejděte dolů do části **diagnostické protokoly** .  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Protokolovací nástroj App Insights":::
1. Zaškrtněte políčko **Povolit** .
1. V rozevíracím seznamu **cíl** vyberte připojeného protokolovacího nástroje.
1. Zadejte **100** jako **vzorkování (%)** a zaškrtněte políčko **vždy protokolovat chyby** .
1. Vyberte **Uložit**.

> [!WARNING]
> Přepsání výchozí hodnoty **0** v **počtu bajtů datové části na protokol** může výrazně snížit výkon vašich rozhraní API.

> [!NOTE]
> Za scénou je na úrovni rozhraní API vytvořená [diagnostická](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entita s názvem ApplicationInsights.

| Název nastavení                        | Typ hodnoty                        | Popis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Povolit                              | boolean                           | Určuje, jestli je povolené protokolování tohoto rozhraní API.                                                                                                                                                                                                                                                                                                |
| Cíl                         | Protokolovací nástroj Azure Application Insights | Určuje protokolovací nástroj Azure Application Insights, který se má použít.                                                                                                                                                                                                                                                                                           |
| Vzorkování (%)                        | decimal                           | Hodnoty od 0 do 100 (procenta). <br/> Určuje procento požadavků, které budou protokolovány Application Insights. 0% vzorkování znamená nulový počet požadavků, zatímco 100% vzorkování znamená, že jsou všechny požadavky protokolovány. <br/> Toto nastavení použijte, pokud chcete snížit výkon při protokolování požadavků na Application Insights. Podívejte [se na výkonnostní důsledky a vzorkování protokolů](#performance-implications-and-log-sampling). |
| Vždycky protokolovat chyby                   | boolean                           | Pokud je toto nastavení vybrané, všechna selhání se budou protokolovat Application Insights bez ohledu na nastavení **vzorkování** .   
| Protokolovat IP adresu klienta | |  Pokud je toto nastavení vybrané, IP adresa klienta pro požadavky rozhraní API se zaprotokoluje Application Insights.                                         |
| Podrobnosti         |                                   | Určuje úroveň podrobností. Budou protokolována pouze vlastní trasování s vyšší úrovní závažnosti. Výchozí: informace.      | 
| Korelační protokol |  |  Vyberte protokol, který se používá ke korelaci telemetrie odesílané více komponentami. Výchozí: starší verze <br/>Informace najdete v tématu [korelace telemetrie v Application Insights](../azure-monitor/app/correlation.md).  |
| Základní možnosti: hlavičky, které se mají protokolovat              | list                              | Určuje hlavičky, které se budou protokolovat Application Insights pro žádosti a odpovědi.  Výchozí: nejsou protokolována žádná záhlaví.                                                                                                                                                                                                             |
| Základní možnosti: počet bajtů datové části, které se mají protokolovat | integer                           | Určuje, kolik prvních bajtů textu je protokolováno, aby Application Insights pro žádosti a odpovědi.  Výchozí hodnota: 0.                                                                                                                                                                                                    |
| Rozšířené možnosti: front-end požadavek  |                                   | Určuje, zda a jak budou do Application Insights protokolovány *požadavky front-end* . *Požadavek front-end* je požadavek příchozí na službu Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: front-endové odpovědi |                                   | Určuje, jestli a jak se mají protokolovat *odpovědi na front-endu* Application Insights. *Front-end* odpověď je odpověď odchozí ze služby Azure API Management.                                                                                                                                                                   |
| Rozšířené možnosti: back-end požadavek   |                                   | Určuje, jestli se budou do Application Insights protokolovat *požadavky back-endu* . *Požadavek back-end* je požadavek, který je odchozí ze služby Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: odpověď back-endu  |                                   | Určuje, jestli se mají do Application Insights protokolovat *odpovědi back-endu* . *Odpověď back-endu* je odpověď příchozí do služby Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Protokolovací nástroje můžete zadat na různých úrovních – jeden protokolovací nástroj rozhraní API nebo protokolovací nástroj pro všechna rozhraní API.
>  
> Pokud zadáte obě:
> + Pokud se jedná o různé protokolovací nástroje, použijí se obě z nich (multiplexované protokoly),
> + Pokud se jedná o stejné protokolovací nástroje, ale mají různá nastavení, pak jeden pro jedno rozhraní API (podrobnější úroveň) přepíše jednu pro všechna rozhraní API.

## <a name="what-data-is-added-to-application-insights"></a>Jaká data jsou přidaná do Application Insights

Application Insights přijímá:

+ *Vyžádat* položku telemetrie pro každý příchozí požadavek (*žádost front-end*, *odpověď front-endu*)
+ Položka telemetrie *závislosti* pro každý požadavek předaný do back-endu (*žádost back*-end, *odpověď back-endu*)
+ *Výjimka* položky telemetrie pro všechny neúspěšné žádosti:
    + Nepovedlo se kvůli uzavřenému připojení klienta.
    + aktivoval se oddíl *On-Error* zásad rozhraní API.
    + má stavový kód odpovědi HTTP, který odpovídá 4xx nebo 5xx.
+ *Sleduje* položku telemetrie, pokud konfigurujete zásady [trasování](api-management-advanced-policies.md#Trace) . `severity`Nastavení v `trace` zásadách musí být rovno nebo větší než `verbosity` nastavení v protokolování Application Insights.

> [!NOTE]
> Informace o maximální velikosti a počtu metrik a událostí na instanci Application Insights najdete v části [Application Insights omezení](../azure-monitor/service-limits.md#application-insights) .

## <a name="performance-implications-and-log-sampling"></a>Důsledky výkonu a vzorkování protokolů

> [!WARNING]
> Protokolování všech událostí může mít vážný dopad na výkon v závislosti na rychlosti příchozích požadavků.

Na základě interních zátěžových testů Tato funkce způsobila snížení propustnosti 40% až 50%, když frekvence požadavků překročí 1 000 požadavků za sekundu. Application Insights je navržená tak, aby používala statistickou analýzu pro vyhodnocení výkonných údajů aplikací. Nejedná se o systém auditu a není vhodný pro protokolování jednotlivých požadavků na rozhraní API s vysokým objemem.

Počet požadavků, které se protokolují, můžete ovlivnit úpravou nastavení **vzorkování** (viz předchozí kroky). Hodnota 100% znamená, že všechny požadavky jsou protokolovány, zatímco 0% neodráží protokolování. **Vzorkování** pomáhá snižovat objem telemetrie a efektivně brání výraznému snížení výkonu, ale stále přináší výhody protokolování.

Přeskočení protokolování hlaviček a textu požadavků a odpovědí bude mít také kladný dopad na zmírnění problémů s výkonem.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [Azure Application Insights](/azure/application-insights/).
+ Zvažte [protokolování pomocí Event Hubs Azure](api-management-howto-log-event-hubs.md).
