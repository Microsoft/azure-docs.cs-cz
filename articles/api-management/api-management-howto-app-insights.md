---
title: Integrace správy rozhraní Azure API s Přehledy aplikací Azure
titleSuffix: Azure API Management
description: Zjistěte, jak protokolovat a zobrazovat události ze správy rozhraní Azure API v Azure Application Insights.
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
ms.openlocfilehash: 48a83fad3395f6ecf06fb1f1ba95aa1b06a53431
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259132"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Postup při integraci služby Azure API Management s Azure Application Insights

Azure API Management umožňuje snadnou integraci s Azure Application Insights – rozšiřitelnou službou pro webové vývojáře, kteří budují a spravují aplikace na více platformách. Tato příručka provede každý krok takové integrace a popisuje strategie pro snížení dopadu na výkon na instanci služby api management.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto průvodce, musíte mít instanci Azure API Management. Pokud ho nemáte, dokončete [nejprve kurz.](get-started-create-service-instance.md)

## <a name="create-an-azure-application-insights-instance"></a>Vytvoření instance Azure Application Insights

Než budete moci použít Azure Application Insights, musíte nejprve vytvořit instanci služby.

1. Otevřete **portál Azure** a přejděte na **Přehledy aplikací**.  
    ![Vytváření přehledů aplikací](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klikněte na tlačítko **+ Přidat**.  
    ![Vytváření přehledů aplikací](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Vyplňte formulář. Jako typ **aplikace**vyberte **možnost Obecné** .
4. Klikněte na **Vytvořit**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Vytvoření připojení mezi Azure Application Insights a instancí služby Azure API Management

1. Přejděte na **instanci služby Azure API Management** na **webu Azure Portal**.
2. V nabídce vlevo vyberte **Application Insights.**
3. Klikněte na tlačítko **+ Přidat**.  
    ![Logger Přehledů aplikací](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Vyberte dříve vytvořenou instanci **Application Insights** a poskytněte krátký popis.
5. Klikněte na **Vytvořit**.
6. Právě jste vytvořili nástrojovací nástroj Azure Application Insights s klíčem instrumentace. Nyní by se měl objevit v seznamu.  
    ![Logger Přehledů aplikací](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Za scénou je vytvořena entita [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger/createorupdate) v instanci správy rozhraní API, která obsahuje klíč instrumentace instance Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Povolení protokolování přehledů aplikací pro rozhraní API

1. Přejděte na **instanci služby Azure API Management** na **webu Azure Portal**.
2. V nabídce na levé straně vyberte **Rozhraní API**.
3. Klikněte na vaše API, v tomto případě **Demo Konference API**.
4. Přejděte na kartu **Nastavení** z horního panelu.
5. Přejděte dolů do části **Protokoly diagnostiky.**  
    ![Logger Přehledů aplikací](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Zaškrtněte políčko **Povolit.**
7. V rozevíracím poli **Cíl** vyberte připojený záznamník.
8. Zadejte **100** jako **vzorkování (%)** a zaškrtněte políčko **Vždy protokolovat chyby.**
9. Klikněte na **Uložit**.

> [!WARNING]
> Přepsání výchozí hodnoty **0** v **poli První bajty těla** může výrazně snížit výkon vašich api.

> [!NOTE]
> Za scénou je vytvořena [diagnostická](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entita s názvem Applicationinsights na úrovni rozhraní API.

| Název nastavení                        | Typ hodnoty                        | Popis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Povolení                              | Boolean                           | Určuje, zda je povoleno protokolování tohoto rozhraní API.                                                                                                                                                                                                                                                                                                |
| Cíl                         | Protokolovací nástroj Azure Application Insights | Určuje nástrojový přehled y Azure Application Insights, který má být použit.                                                                                                                                                                                                                                                                                           |
| Odběr vzorků (%)                        | decimal                           | Hodnoty od 0 do 100 (procent). <br/> Určuje, jaké procento požadavků bude zaznamenáno do Azure Application Insights. 0% vzorkování znamená nulové protokolované požadavky, zatímco 100% vzorkování znamená všechny protokolované požadavky. <br/> Toto nastavení se používá ke snížení důsledků protokolování požadavků na protokolování do Azure Application Insights (viz část níže). |
| Vždy protokolovat chyby                   | Boolean                           | Pokud je toto nastavení vybráno, všechny chyby se zaprotokolují do Azure Application Insights, bez ohledu na nastavení **vzorkování.**                                                                                                                                                                                                                  |
| Základní možnosti: Záhlaví              | list                              | Určuje záhlaví, která budou zaznamenána do Azure Application Insights pro požadavky a odpovědi.  Výchozí: nejsou protokolovány žádné hlavičky.                                                                                                                                                                                                             |
| Základní možnosti: První bajty těla  | celé číslo                           | Určuje, kolik prvních bajtů těla se zaprotokoluje do Azure Application Insights pro požadavky a odpovědi.  Výchozí: tělo není zaznamenáno.                                                                                                                                                                                                    |
| Rozšířené možnosti: Podrobnost         |                                   | Určuje úroveň podrobností. Budou zaznamenány pouze vlastní trasování s vyšší úrovní závažnosti. Výchozí: Informace.                                                                                                                                                                                                                               |
| Rozšířené možnosti: Požadavek na front-end  |                                   | Určuje, zda a jak budou *požadavky front-endu* protokolovány do Azure Application Insights. *Front-end požadavek* je požadavek příchozí do služby Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: Front-endová odezva |                                   | Určuje, zda a jak budou *odpovědi front-endu* protokolovány do Azure Application Insights. *Front-end odpověď* je odpověď odchozí ze služby Azure API Management.                                                                                                                                                                   |
| Rozšířené možnosti: Požadavek na back-end   |                                   | Určuje, zda a jak budou *back-endové požadavky* protokolovány do Azure Application Insights. *Back-endový požadavek* je požadavek odchozí ze služby Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: Back-endová odpověď  |                                   | Určuje, zda a jak budou *back-endové odpovědi* protokolovány do Azure Application Insights. *Back-endová odpověď* je odpověď přicházející do služby Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Můžete zadat úhozy kláves na různých úrovních – jeden protokolovací nástroj API nebo protokolování pro všechna rozhraní API.
>  
> Pokud zadáte obojí:
> + pokud se jedná o různé úhozy kláves, budou použity oba (multiplexní protokoly),
> + Pokud jsou stejné úhozy kláves, ale mají různá nastavení, pak jeden pro jednu api (podrobnější úroveň) přepíše jeden pro všechna rozhraní API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jaká data se přidají do Azure Application Insights

Azure Application Insights přijímá:

+ *Požadavek* na položku telemetrie pro každý příchozí požadavek (*požadavek frontendu*, *odpověď frontendu*),
+ Položka telemetrie *závislostí* pro každý požadavek předaný do back-endové služby *(požadavek back-endu*, *back-endová odpověď),*
+ Položka telemetrie *výjimky* pro každý neúspěšný požadavek.

Neúspěšný požadavek je požadavek, který:

+ se nezdařilo z důvodu uzavřeného připojení klienta, nebo
+ spustila část zásad rozhraní API *o chybách* nebo
+ má odpověď HTTP stavový kód odpovídající 4xx nebo 5xx.

## <a name="performance-implications-and-log-sampling"></a>Důsledky pro výkon a vzorkování protokolů

> [!WARNING]
> Protokolování všech událostí může mít vážné důsledky pro výkon v závislosti na rychlosti příchozích požadavků.

Na základě interních zátěžových testů způsobilo povolení této funkce 40%-50% snížení propustnosti, když rychlost požadavků překročila 1 000 požadavků za sekundu. Azure Application Insights je navržený tak, aby používal statistické analýzy pro hodnocení výkonu aplikací. Není určen k auditu systému a není vhodný pro protokolování každý jednotlivý požadavek pro velkoobjemová api.

Můžete manipulovat s počtem požadavků, které jsou protokolovány úpravou nastavení **vzorkování** (viz výše uvedené kroky). Hodnota 100 % znamená, že všechny požadavky jsou protokolovány, zatímco 0 % odráží žádné protokolování vůbec. **Vzorkování** pomáhá snížit objem telemetrie, účinně zabraňuje významnému snížení výkonu, zatímco stále nese výhody protokolování.

Přeskočení protokolování záhlaví a tělo požadavků a odpovědí bude mít také pozitivní dopad na zmírnění problémů s výkonem.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Další kroky

+ Další informace o [Přehledech aplikací Azure](https://docs.microsoft.com/azure/application-insights/).
+ Zvažte [protokolování pomocí Azure Event Hubs](api-management-howto-log-event-hubs.md).
