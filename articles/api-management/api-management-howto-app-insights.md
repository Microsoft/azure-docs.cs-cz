---
title: Integrace Azure API Management s využitím Azure Application Insights
titleSuffix: Azure API Management
description: Naučte se protokolovat a zobrazovat události z Azure API Management v Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8c9df3393a0554d2e65b3918c6760885f89e11ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254738"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Postup při integraci služby Azure API Management s Azure Application Insights

Azure API Management umožňuje snadnou integraci se službou Azure Application Insights – rozšiřitelná služba pro webové vývojáře vytvářející a spravované aplikace na různých platformách. Tato příručka vás provede jednotlivými kroky této integrace a popisuje strategie pro snížení dopadu na výkon vaší instance služby API Management.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto průvodce, musíte mít instanci služby Azure API Management. Pokud ho nemáte, dokončete nejprve [kurz](get-started-create-service-instance.md) .

## <a name="create-an-azure-application-insights-instance"></a>Vytvoření instance služby Azure Application Insights

Než budete moct použít Azure Application Insights, musíte nejdřív vytvořit instanci služby.

1. Otevřete **Azure Portal** a přejděte do **Application Insights**.  
    ![Vytvoření App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klikněte na **+ Přidat**.  
    ![Vytvoření App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Vyplňte formulář. Jako **Typ aplikace**vyberte **Obecné** .
4. Klikněte na **Vytvořit**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Vytvoření připojení mezi službou Azure Application Insights a instancí služby Azure API Management

1. V **Azure Portal**přejděte do své **instance služby Azure API Management** .
2. V nabídce vlevo vyberte **Application Insights** .
3. Klikněte na **+ Přidat**.  
    ![Protokolovací nástroj App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Vyberte dříve vytvořenou instanci **Application Insights** a zadejte krátký popis.
5. Klikněte na **Vytvořit**.
6. Právě jste vytvořili protokolovací nástroj pro Azure Application Insights s klíčem instrumentace. Měl by se teď zobrazit v seznamu.  
    ![Protokolovací nástroj App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Za scénou je ve vaší instanci API Management vytvořená entita [protokolovacího](/rest/api/apimanagement/2019-12-01/logger/createorupdate) nástroje obsahující klíč instrumentace instance Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Povolení protokolování Application Insights pro vaše rozhraní API

1. V **Azure Portal**přejděte do své **instance služby Azure API Management** .
2. V nabídce na levé straně vyberte **Rozhraní API**.
3. V tomto případě **ukázkové konferenční rozhraní API**klikněte na své rozhraní API.
4. V horním panelu přejdete na kartu **Nastavení** .
5. Přejděte dolů do části **diagnostické protokoly** .  
    ![Protokolovací nástroj App Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Zaškrtněte políčko **Povolit** .
7. V rozevíracím seznamu **cíl** vyberte připojeného protokolovacího nástroje.
8. Zadejte **100** jako **vzorkování (%)** a zaškrtněte políčko **vždy zaznamenávat chyby** .
9. Klikněte na **Uložit**.

> [!WARNING]
> Přepsání výchozí hodnoty **0** v **prvních bajtech pole body** může výrazně snížit výkon vašich rozhraní API.

> [!NOTE]
> Za scénou je na úrovni rozhraní API vytvořená [diagnostická](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entita s názvem ApplicationInsights.

| Název nastavení                        | Typ hodnoty                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Povolit                              | boolean                           | Určuje, jestli je povolené protokolování tohoto rozhraní API.                                                                                                                                                                                                                                                                                                |
| Cíl                         | Protokolovací nástroj Azure Application Insights | Určuje protokolovací nástroj Azure Application Insights, který se má použít.                                                                                                                                                                                                                                                                                           |
| Vzorkování (%)                        | decimal                           | Hodnoty od 0 do 100 (procenta). <br/> Určuje, jaké procento požadavků se bude protokolovat do služby Azure Application Insights. 0% vzorkování znamená nulový počet požadavků, zatímco 100% vzorkování znamená, že jsou všechny požadavky protokolovány. <br/> Toto nastavení se používá k omezení dopadu na výkon požadavků protokolování do Azure Application Insights (viz část níže). |
| Vždycky protokolovat chyby                   | boolean                           | Pokud je toto nastavení vybrané, všechny chyby se zaprotokolují do služby Azure Application Insights bez ohledu na nastavení **vzorkování** .                                                                                                                                                                                                                  |
| Základní možnosti: hlavičky              | list                              | Určuje hlavičky, které se budou protokolovat do služby Azure Application Insights pro žádosti a odpovědi.  Výchozí: nejsou protokolována žádná záhlaví.                                                                                                                                                                                                             |
| Základní možnosti: první bajty těla  | integer                           | Určuje, kolik prvních bajtů textu se do Azure Application Insights zaznamená pro žádosti a odpovědi.  Výchozí: tělo není protokolováno.                                                                                                                                                                                                    |
| Rozšířené možnosti: podrobnosti         |                                   | Určuje úroveň podrobností. Budou protokolována pouze vlastní trasování s vyšší úrovní závažnosti. Výchozí: informace.                                                                                                                                                                                                                               |
| Rozšířené možnosti: front-end požadavek  |                                   | Určuje, jestli a jak se budou *Odeslané požadavky na front-end* protokolovat do Application Insights Azure. *Požadavek front-end* je požadavek příchozí na službu Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: front-endové odpovědi |                                   | Určuje, jestli se mají do Azure Application Insights zaprotokolovat *odpovědi front-endu* . *Front-end* odpověď je odpověď odchozí ze služby Azure API Management.                                                                                                                                                                   |
| Rozšířené možnosti: back-end požadavek   |                                   | Určuje, jestli se budou do Azure Application Insights protokolovat *požadavky back-endu* . *Požadavek back-end* je požadavek, který je odchozí ze služby Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: odpověď back-endu  |                                   | Určuje, jestli se budou *odpovědi na back-end* protokolovat do služby Azure Application Insights. *Odpověď back-endu* je odpověď příchozí do služby Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Protokolovací nástroje můžete zadat na různých úrovních – jeden protokolovací nástroj rozhraní API nebo protokolovací nástroj pro všechna rozhraní API.
>  
> Pokud zadáte obě:
> + Pokud se jedná o různé protokolovací nástroje, použijí se obě z nich (multiplexované protokoly),
> + Pokud se jedná o stejné protokolovací nástroje, ale mají různá nastavení, pak jeden pro jedno rozhraní API (podrobnější úroveň) přepíše jednu pro všechna rozhraní API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jaká data se přidávají do Azure Application Insights

Azure Application Insights přijímá:

+ *Vyžádat* položku telemetrie pro každý příchozí požadavek (*žádost front-end*, *odpověď front-endu*)
+ Položka telemetrie *závislosti* pro každý požadavek předaný do back-endu (*žádost back*-end, *odpověď back-endu*)
+ Položka telemetrie *výjimky* pro všechny neúspěšné žádosti.

Neúspěšný požadavek je požadavek, který:

+ Nepovedlo se kvůli uzavřenému připojení klienta nebo
+ aktivovali jsme oddíl *On-Error* zásad rozhraní API nebo
+ má stavový kód odpovědi HTTP, který odpovídá 4xx nebo 5xx.

## <a name="performance-implications-and-log-sampling"></a>Důsledky výkonu a vzorkování protokolů

> [!WARNING]
> Protokolování všech událostí může mít vážný dopad na výkon v závislosti na rychlosti příchozích požadavků.

Na základě interních zátěžových testů Tato funkce způsobila snížení propustnosti 40% až 50%, když frekvence požadavků překročí 1 000 požadavků za sekundu. Azure Application Insights je navržená tak, aby používala statistickou analýzu pro vyhodnocení výkonných údajů aplikací. Nejedná se o systém auditu a není vhodný pro protokolování jednotlivých požadavků na rozhraní API s vysokým objemem.

Počet požadavků, které se protokolují, můžete ovlivnit úpravou nastavení **vzorkování** (viz postup výše). Hodnota 100% znamená, že všechny požadavky jsou protokolovány, zatímco 0% neodráží žádné protokolování. **Vzorkování** pomáhá snížit objem telemetrie a efektivně tak zabránit výraznému snížení výkonu, ale stále přináší výhody protokolování.

Přeskočení protokolování hlaviček a textu požadavků a odpovědí bude mít také kladný dopad na zmírnění problémů s výkonem.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [Azure Application Insights](/azure/application-insights/).
+ Zvažte [protokolování pomocí Event Hubs Azure](api-management-howto-log-event-hubs.md).
