---
title: Postup pro integraci Azure API Management Azure Application Insights | Microsoft Docs
description: Naučte se přihlásit a zobrazit události z Azure API Management ve službě Azure Application Insights.
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
ms.openlocfilehash: 7740da505f7635944536252d60ec2c2039295975
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320580"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Postup pro integraci Azure API Management Azure Application Insights

Azure API Management umožňuje snadnou integraci s Azure Application Insights - rozšiřitelnou službu vývojářům webů vytváření a správě aplikací ve více platformách. Tento průvodce provede každý krok takové integrace a popisuje strategií pro snížení vlivu na výkon na instanci služby API Management.

## <a name="prerequisites"></a>Požadavky

Podle této příručky, musíte mít instanci Azure API Management. Pokud nemáte, proveďte [kurzu](get-started-create-service-instance.md) první.

## <a name="create-an-azure-application-insights-instance"></a>Vytvoření instance služby Azure Application Insights

Před použitím Azure Application Insights, musíte nejprve vytvořit instanci služby.

1. Otevřete **portál Azure** a přejděte do **Application Insights**.  
    ![Vytvoření statistiky aplikace](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klikněte na tlačítko **+ Přidat**.  
    ![Vytvoření statistiky aplikace](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Vyplnění formuláře. Vyberte **Obecné** jako **typ aplikace**.
4. Klikněte na možnost **Vytvořit**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Vytvořte připojení mezi Azure Application Insights a instance služby Azure API Management

1. Přejděte do vaší **instance služby Azure API Management** v **portál Azure**.
2. Vyberte **Application Insights** z nabídky na levé straně.
3. Klikněte na tlačítko **+ Přidat**.  
    ![Protokolovač statistiky aplikace](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Vyberte dříve vytvořenou **Application Insights** instance a zadejte krátký popis.
5. Klikněte na možnost **Vytvořit**.
6. S klíčem instrumentace jste právě vytvořili protokolovač Azure Application Insights. Nyní se má zobrazit v seznamu.  
    ![Protokolovač statistiky aplikace](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

## <a name="enable-application-insights-logging-for-your-api"></a>Povolit protokolování Application Insights pro vaše rozhraní API

1. Přejděte do vaší **instance služby Azure API Management** v **portál Azure**.
2. Vyberte **rozhraní API** z nabídky na levé straně.
3. Klikněte na rozhraní API, v takovém případě **ukázku konferenční API**.
4. Přejděte na **nastavení** kartě z horním panelu.
5. Přejděte dolů k položce **protokolů diagnostiky** části.  
    ![Protokolovač statistiky aplikace](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Zkontrolujte **povolit** pole.
7. Vyberte vaše připojené protokoly v **cílové** rozevíracího seznamu.
8. Vstup **100** jako **vzorkování (%)** a osové **chyby v protokolu vždy** zaškrtávací políčko.
9. Vstup **1024** v **první bajty těla** pole.
10. Klikněte na **Uložit**.

| Název nastavení                        | Typ hodnoty                        | Popis                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Povolení                              | Boolean                           | Určuje, zda je povoleno protokolování toto rozhraní API.                                                                                                                                                                                                                                                                                                |
| Cíl                         | Azure protokolovacího nástroje Application Insights | Určuje protokolovacího nástroje Azure Application Insights, který se má použít                                                                                                                                                                                                                                                                                           |
| Vzorkování (%)                        | Decimal                           | Hodnoty od 0 do 100 (procent). <br/> Určuje, jaké procento žádostí, budou zaznamenány do služby Azure Application Insights. 0 % vzorkování znamená nulové žádosti o přihlášení, zatímco vzorkování 100 % znamená všechny žádosti o přihlášení. <br/> Toto nastavení se používá ke snížení výkonu dopad protokolování žádostí o služby Azure Application Insights (viz následující části). |
| Vždy chyby v protokolu                   | Boolean                           | Pokud je toto nastavení vybrané, všechny chyby budou zaznamenány do služby Azure Application Insights, bez ohledu na to **vzorkování** nastavení.                                                                                                                                                                                                                  |
| Základní možnosti: hlavičky              | Seznam                              | Určuje seznam hlaviček, které se protokolují do služby Azure Application Insights pro požadavky a odpovědi.  Výchozí: žádná záhlaví přihlášeni.                                                                                                                                                                                                             |
| Základní možnosti: První bajty textu  | integer                           | Určuje, kolik první bajty obsahu se protokolují do služby Azure Application Insights pro požadavky a odpovědi.  Výchozí hodnota: text se neprotokolují.                                                                                                                                                                                              |
| Rozšířené možnosti: Požadavek front-endu  |                                   | Určuje, zda a jak *front-endu požadavky* se zaznamená do služby Azure Application Insights. *Žádost o front-endu* je žádost o příchozí ke službě Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: Odpověď front-endu |                                   | Určuje, zda a jak *front-endu odpovědí* se zaznamená do služby Azure Application Insights. *Front-endu odpovědi* je odchozí odpověď ze služby Azure API Management.                                                                                                                                                                   |
| Rozšířené možnosti: Požadavek back-end   |                                   | Určuje, zda a jak *back-end požadavky* se zaznamená do služby Azure Application Insights. *Požadavek back-end* je žádost o odchozí ze služby Azure API Management.                                                                                                                                                                        |
| Rozšířené možnosti: Odpověď back-end  |                                   | Určuje, zda a jak *odezvy back-endu* se zaznamená do služby Azure Application Insights. *Back-end odpovědi* je odpověď příchozí ke službě Azure API Management.                                                                                                                                                                       |

> [!NOTE]
> Protokolovací nástroje můžete zadat na různých úrovních – jeden protokoly rozhraní API nebo protokolovacího nástroje pro všechna rozhraní API.
>  
> Pokud zadáte oba:
> + Pokud jsou jiné protokolovacích nástrojů, pak oba dva použije (multiplexní protokolů),
> + Pokud jsou stejné protokolovacích nástrojů ale mají různá nastavení a pak jednu pro jediného rozhraní API (podrobnější úrovni) jeden pro všechna rozhraní API se přepíše.

## <a name="what-data-is-added-to-azure-application-insights"></a>Jaká data se přidá do služby Azure Application Insights

Obdrží Azure Application Insights:

+ *Žádost o* telemetrie položku pro každou příchozí žádost (*front-endu požadavek*, *front-endu odpovědi*),
+ *Závislost* telemetrie položku pro každou žádost předávaných do back-end službu (*požadavek back-end*, *back-end odpovědi*),
+ *Výjimka* telemetrie položku pro každou žádost se nezdařila.

Chybné žádosti je požadavek, který:

+ se nezdařilo z důvodu připojení uzavřené klienta, nebo
+ aktivaci *při chybě* část rozhraní API zásad, nebo
+ má odpověď HTTP stavový kód odpovídající 4xx nebo 5xx.

## <a name="performance-implications-and-log-sampling"></a>Ovlivnit výkon a protokolu vzorkování

> [!WARNING]
> Všechny události protokolování může mít ovlivní výkon, v závislosti na počet příchozích požadavků.

Podle toho, interní zátěžových testů, povolení této funkce způsobila snížení 40 % – 50 % propustnost při překročení rychlost požadavků 1000 požadavků za sekundu. Azure Application Insights je určena pro použití statistické analýzy pro posuzování přínos aplikace. Je tudíž auditu systému a není vhodná pro protokolování každé jednotlivé žádosti pro rozhraní API vysoký počet.

Můžete upravit počet požadavků protokolována úpravou **vzorkování** nastavení (viz výše uvedených kroků). Hodnota 100 % znamená, že všechny žádosti o přihlášení, při 0 % odráží žádné protokolování vůbec. **Vzorkování** pomáhá snížit objem telemetrických dat, efektivně brání snížení významné výkonu při stále dělal výhod protokolování.

Přeskočení protokolování hlaviček a text žádosti a odpovědi bude mít kladný vliv na boj problémy s výkonem.

## <a name="next-steps"></a>Další postup

+ Další informace o [Azure Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/).
+ Vezměte v úvahu [protokolování s Azure Event Hubs](api-management-howto-log-event-hubs.md).
