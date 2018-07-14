---
title: Jak integrovat Azure API Management s využitím Azure Application Insights | Dokumentace Microsoftu
description: Zjistěte, jak protokolování a zobrazení událostí ze služby Azure API Management ve službě Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8546b1228c8d8f213cb87692144e8d1d31a949d8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001799"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Jak integrovat Azure API Management s využitím Azure Application Insights

Azure API Management umožňuje jednoduchou integraci s Azure Application Insights – rozšiřitelnou službu pro webové vývojáře, vytvářet a spravovat aplikace na různých platformách. Tento průvodce vás provede všemi kroky tato integrační a popisuje strategie pro snížení vlivu na výkon v instanci služby API Management.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle tohoto průvodce, musíte mít instanci Azure API Management. Pokud ho nemáte, proveďte [kurzu](get-started-create-service-instance.md) první.

## <a name="create-an-azure-application-insights-instance"></a>Vytvoření instance Azure Application Insights

Než budete moct použít Azure Application Insights, musíte nejprve vytvořit instanci služby.

1. Otevřít **webu Azure portal** a přejděte do **Application Insights**.  
    ![Vytvořit App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klikněte na tlačítko **+ Přidat**.  
    ![Vytvořit App Insights](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Vyplňte formulář. Vyberte **Obecné** jako **typ aplikace**.
4. Klikněte na možnost **Vytvořit**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Vytvořte připojení mezi Azure Application Insights a instance služby Azure API Management

1. Přejděte do vaší **instance služby Azure API Management** v **webu Azure portal**.
2. Vyberte **Application Insights** z nabídky na levé straně.
3. Klikněte na tlačítko **+ Přidat**.  
    ![Protokolovací nástroj App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Vyberte dříve vytvořenou **Application Insights** instance a zadejte krátký popis.
5. Klikněte na možnost **Vytvořit**.
6. Kterou jste právě vytvořili protokolovač Azure Application Insights s instrumentačním klíčem. Měl by se nyní zobrazit v seznamu.  
    ![Protokolovací nástroj App Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Za scénu [protokolovací nástroj](https://docs.microsoft.com/en-us/rest/api/apimanagement/logger/createorupdate) entita se vytvoří ve vaší instanci API Management, který obsahuje klíč instrumentace instance Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Povolení protokolování Application Insights pro vaše rozhraní API

1. Přejděte do vaší **instance služby Azure API Management** v **webu Azure portal**.
2. Vyberte **rozhraní API** z nabídky na levé straně.
3. Klikněte na vaše rozhraní API, v tomto případě **Demo Conference API**.
4. Přejděte **nastavení** karty v horním panelu.
5. Přejděte dolů k položce **diagnostické protokoly** oddílu.  
    ![Protokolovací nástroj App Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Zkontrolujte, **povolit** pole.
7. Vyberte vaše připojené protokolovací nástroj v **cílové** rozevíracího seznamu.
8. Vstup **100** jako **vzorkování (%)** a osové **vždy protokolovat chyby** zaškrtávací políčko.
9. Vstup **1024** v **první bajty těla** pole.
10. Klikněte na **Uložit**.

> [!NOTE]
> Za scénu [diagnostických](https://docs.microsoft.com/en-us/rest/api/apimanagement/diagnostic/createorupdate) entitu s názvem "applicationinsights" se vytváří na úrovni rozhraní API.

| Název nastavení                        | Typ hodnoty                        | Popis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Povolení                              | Boolean                           | Určuje, zda je povoleno protokolování tohoto rozhraní API.                                                                                                                                                                                                                                                                                                |
| Cíl                         | Protokolovací nástroj Azure Application Insights | Určuje protokolovací nástroj Azure Application Insights, který se má použít                                                                                                                                                                                                                                                                                           |
| Vzorkování (%)                        | Decimal                           | Hodnoty od 0 do 100 (procent). <br/> Určuje, jaké procento žádostí se budou protokolovat do služby Azure Application Insights. vzorkování 0 % znamená, že žádný žádosti o přihlášení, zatímco vzorkování 100 % znamená, že všechny žádosti o přihlášení. <br/> Toto nastavení se používá ke snížení výkonu důsledcích protokolování žádostí do služby Azure Application Insights (viz níže uvedené části). |
| Vždy protokolovat chyby                   | Boolean                           | Pokud je toto nastavení vybrané, všechny chyby se budou protokolovat do služby Azure Application Insights, bez ohledu **vzorkování** nastavení.                                                                                                                                                                                                                  |
| Základní možnosti: záhlaví              | Seznam                              | Určuje hlavičky, které se budou protokolovat do služby Azure Application Insights pro požadavky a odpovědi.  Výchozí hodnota: žádné hlavičky jsou protokolovány.                                                                                                                                                                                                             |
| Základní možnosti: První bajty z textu  | integer                           | Určuje, kolik první bajty textu se protokolují do služby Azure Application Insights pro požadavky a odpovědi.  Výchozí: text se neprotokolují.                                                                                                                                                                                              |
| Rozšířené možnosti: Požadavek front-endu  |                                   | Určuje, zda a jak *front-endu požadavky* se budou protokolovat do služby Azure Application Insights. *Front-endu požadavek* je žádost o příchozích ke službě Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: Odpověď front-endu |                                   | Určuje, zda a jak *front-endu odpovědi* se budou protokolovat do služby Azure Application Insights. *Front-endu odpovědi* je odchozí odpověď ze služby Azure API Management.                                                                                                                                                                   |
| Rozšířené možnosti: Požadavek back-endu   |                                   | Určuje, zda a jak *back-endových požadavků* se budou protokolovat do služby Azure Application Insights. *Požadavek back-endu* ze služby Azure API Management je odchozí požadavek.                                                                                                                                                                        |
| Rozšířené možnosti: Odpovědi back-endu  |                                   | Určuje, zda a jak *odpovědi back-endu* se budou protokolovat do služby Azure Application Insights. *Odpovědi back-endu* je odpověď na příchozí ke službě Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Zadejte protokolovací nástroje na různých úrovních – jeden protokolovacího nástroje rozhraní API nebo protokolovací nástroj pro všechna rozhraní API.
>  
> Pokud zadáte obě:
> + Pokud jsou různé Protokolovací nástroje, pak obou z nich se použije (multiplexing protokoly)
> + Pokud jsou stejné protokolovacích nástrojů ale mají různá nastavení a jeden pro jedno rozhraní API (podrobnější úrovni) se přepíše, jeden pro všechna rozhraní API.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jaká data se přidá do služby Azure Application Insights

Azure Application Insights přijme:

+ *Žádost o* položky telemetrie pro každého příchozího požadavku (*front-endu požadavek*, *front-endu odpovědi*),
+ *Závislost* položky telemetrie pro každý požadavek předá do back-end službu (*požadavek back-endu*, *odpovědi back-endu*),
+ *Výjimka* položky telemetrie pro každé neúspěšné žádosti.

Chybné žádosti je požadavkem, který:

+ se nezdařilo z důvodu připojení uzavřené klienta, nebo
+ Aktivované *při chybě* části zásad rozhraní API nebo
+ má odpovědí stavový kód HTTP odpovídající 4xx a 5xx.

## <a name="performance-implications-and-log-sampling"></a>Vliv na výkon a protokolu vzorkování

> [!WARNING]
> Protokolování všech událostí může mít vliv na výkon, v závislosti na počet příchozích požadavků.

Založené na interní zátěžové testy, povolení této funkce způsobila 40 – 50 % snížení propustnost při 1 000 požadavků za sekundu žádostí. Azure Application Insights je navržen pro použití statistické analýzy pro posuzování funkční aplikaci. Tudíž auditovat systém a není vhodná pro každý jednotlivý požadavek pro rozhraní API, vysoký počet protokolování.

Můžete upravit počet požadavků, které jsou právě protokolovány úpravou **vzorkování** nastavení (viz výše uvedené kroky). Hodnota je 100 % znamená, že všechny žádosti o přihlášení, zatímco 0 % odráží žádné protokolování vůbec. **Vzorkování** pomáhá snížit objem telemetrických dat, efektivně brání významné snížení výkonu, při stále o výhodách protokolování výkonu.

Přeskakuje protokolování záhlaví a text žádosti a odpovědi bude také mít kladný dopad na boj problémy s výkonem.

## <a name="next-steps"></a>Další postup

+ Další informace o [Azure Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/).
+ Vezměte v úvahu [protokolování pomocí služby Azure Event Hubs](api-management-howto-log-event-hubs.md).
