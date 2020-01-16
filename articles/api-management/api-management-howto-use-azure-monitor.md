---
title: Monitorování publikovaných rozhraní API pomocí služby Azure API Management | Microsoft Docs
description: Pomocí kroků v tomto kurzu se naučíte monitorovat rozhraní API pomocí služby Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: b06301ab424a29d8f0e31e8f4dee26265327896b
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028367"
---
# <a name="monitor-published-apis"></a>Monitorování publikovaných rozhraní API

Prostřednictvím služby Azure Monitor můžete vizualizovat metriky nebo protokoly pocházející z prostředků Azure, zadávat na ně dotazy, směrovat je, archivovat je a provádět s nimi příslušné akce.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazení protokolů aktivit
> * Zobrazit diagnostické protokoly
> * Zobrazit metriky rozhraní API 
> * Nastavit pravidlo upozornění při neoprávněných voláních vašeho rozhraní API

Následující video ukazuje, jak pomocí služby Azure Monitor monitorovat službu API Management. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Zobrazení metrik vašich rozhraní API

API Management každou minutu vysílá metriky, takže vám skoro v reálném čase poskytuje přehled o stavu vašich rozhraní API. Toto je souhrn některých dostupných metrik:

* Kapacita: pomáhá při rozhodování o upgradu/downgrade služeb APIM Services. Metrika se generuje každou minutu a odráží kapacitu brány v čase vytvoření sestavy. Její hodnoty se pohybují v rozsahu od 0 do 100 a počítají se na základě prostředků brány, jako je využití procesoru nebo paměti.
* Celkový počet požadavků brány: počet požadavků rozhraní API za dané období. 
* Úspěšné požadavky brány: počet požadavků rozhraní API, které obdržely kódy úspěšné odpovědi HTTP včetně kódů 304, 307 a veškerých kódů menších než 301 (například 200).
* Neúspěšné požadavky brány: počet požadavků rozhraní API, které obdržely kódy chybné odpovědi HTTP včetně kódů 400 a veškerých kódů větších než 500.
* Neoprávněné požadavky brány: počet požadavků rozhraní API, které obdržely kódy odpovědi HTTP jako 401, 403 a 429.
* Ostatní požadavky brány: počet požadavků rozhraní API, které obdržely kódy odpovědi HTTP nepatřící do žádné z výše uvedených skupin (například 418).

![Graf metrik](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Přístup k metrikám:

1. V nabídce ve spodní části stránky vyberte **Metriky**.

    ![metriky](./media/api-management-azure-monitor/api-management-metrics-blade.png)

1. V rozevíracím seznamu vyberte metriky, které vás zajímají. Například **požadavky**. 
1. Graf zobrazí celkový počet volání rozhraní API.
1. Graf lze filtrovat pomocí dimenzí metriky **požadavků** . Klikněte například na **Přidat filtr**, vyberte **kód odpovědi back-endu**a jako hodnotu zadejte 500. Graf teď zobrazuje počet požadavků, které se v back-endu rozhraní API nezdařily.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Nastavení pravidla upozornění při neoprávněných požadavcích

Můžete nakonfigurovat odesílání upozornění na základě metrik a protokolů aktivit. Azure Monitor umožňuje nakonfigurovat upozornění, které při aktivaci provede některé z následujících kroků:

* Odeslání e-mailového oznámení
* Volání webhooku
* Vyvolání aplikace logiky Azure

Konfigurace upozornění:

1. V řádku nabídek poblíž dolního okraje stránky vyberte **výstrahy** .

    ![výstrahy](./media/api-management-azure-monitor/alert-menu-item.png)

2. Klikněte na **nové pravidlo výstrahy** pro tuto výstrahu.
3. Klikněte na **Přidat podmínku**.
4. V rozevíracím seznamu typ signálu vyberte **metriky** .
5. Jako signál, který chcete monitorovat, vyberte **neautorizovaný požadavek brány** .

    ![výstrahy](./media/api-management-azure-monitor/signal-type.png)

6. V zobrazení **Konfigurovat logiku signálu** zadejte prahovou hodnotu, po které se má výstraha aktivovat, a klikněte na **Hotovo**.

    ![výstrahy](./media/api-management-azure-monitor/threshold.png)

7. Vyberte existující skupinu akcí nebo vytvořte novou. V následujícím příkladu se pošle e-mailem správci. 

    ![výstrahy](./media/api-management-azure-monitor/action-details.png)

8. Zadejte název, popis pravidla výstrahy a vyberte úroveň závažnosti. 
9. Stiskněte **vytvořit pravidlo upozornění**.
10. Nyní se pokuste zavolat rozhraní API pro konferenci bez klíče rozhraní API. Tato výstraha se aktivuje a správci se pošle e-mail. 

## <a name="activity-logs"></a>Protokoly aktivit

Protokoly aktivit poskytují přehled o operacích provedených vašimi službami API Management. Pomocí protokolů aktivit můžete zjistit obsah, autora a čas veškerých operací zápisu (PUT, POST, DELETE) provedených ve vašich službách API Management.

> [!NOTE]
> Protokoly aktivit nezahrnují operace čtení (GET) ani operace prováděné na webu Azure Portal nebo pomocí původních rozhraní API pro správu.

Protokoly aktivit můžete zobrazit ve své službě API Management nebo k nim můžete získat přístup ze všech svých prostředků Azure prostřednictvím služby Azure Monitor. 

![Protokoly aktivit](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Zobrazení protokolů aktivit:

1. Vyberte instanci služby APIM.
2. Klikněte na **Protokol aktivit**.

    ![Protokol aktivit](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Vyberte požadovaný obor filtrování a klikněte na **Použít**.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Diagnostické protokoly poskytují spoustu informací o operacích a chybách, které jsou důležité pro audit i pro účely odstraňování potíží. Diagnostické protokoly se liší od protokolů aktivit. Protokoly aktivit poskytují přehled o operacích provedených ve vašich prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které provedly vaše prostředky.

Konfigurace diagnostických protokolů:

1. Vyberte instanci služby APIM.
2. Klikněte na **Nastavení diagnostiky**.

    ![Diagnostické protokoly](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Klikněte na **Zapnout diagnostiku**. Diagnostické protokoly můžete archivovat spolu s metrikami v účtu úložiště, streamovat je do centra událostí nebo je odeslat do protokolů Azure Monitor. 

API Management v současné době poskytuje diagnostické protokoly (dávkované po hodinách) o jednotlivých požadavcích rozhraní API, kde má každá položka následující schéma:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Vlastnost  | Typ | Popis |
| ------------- | ------------- | ------------- |
| isRequestSuccess | Boolean | Má hodnotu true, pokud se požadavek HTTP dokončil se stavovým kódem odpovědi v rozsahu 2xx nebo 3xx. |
| time | date-time | Časové razítko, kdy brána spouští zpracování žádosti |
| operationName | string | Konstantní hodnota Microsoft.ApiManagement/GatewayLogs. |
| category | string | Konstantní hodnota GatewayLogs. |
| durationMs | celé číslo | Počet milisekund od chvíle, kdy brána přijala požadavek, do úplného odeslání odpovědi. Zahrnuje clienTime, cacheTime a webčas_ukončení. |
| callerIpAddress | string | IP adresa bezprostředního volajícího brány (může být prostředníkem). |
| correlationId | string | Jedinečný identifikátor požadavku HTTP přiřazený službou API Management. |
| location | string | Název oblasti Azure, ve které se nachází brána, která požadavek zpracovala. |
| httpStatusCodeCategory | string | Kategorie stavového kódu odpovědi HTTP: Úspěch (301 nebo nižší, 304 nebo 307), Neautorizováno (401, 403, 429), Chyba (400, 500 až 600), Jiné. |
| resourceId | string | ID\<předplatného API Management Resource/SUBSCRIPTIONS/>/RESOURCEGROUPS/\<Resource-Group >/PROVIDERS/MICROSOFT. Název APIMANAGEMENT/SERVICE/\< |
| properties | object | Vlastnosti aktuálního požadavku. |
| method | string | Metoda HTTP příchozího požadavku. |
| url | string | Adresa URL příchozího požadavku. |
| clientProtocol | string | Verze protokolu HTTP příchozího požadavku. |
| responseCode | celé číslo | Stavový kód odpovědi HTTP odeslané do klienta. |
| backendMethod | string | Metoda HTTP požadavku odeslaného do back-endu. |
| backendUrl | string | Adresa URL požadavku odeslaného do back-endu. |
| backendResponseCode | celé číslo | Kód odpovědi HTTP přijaté z back-endu. |
| backendProtocol | string | Verze protokolu HTTP požadavku odeslaného do back-endu. | 
| requestSize | celé číslo | Počet bajtů přijatých z klienta během zpracování požadavku. | 
| responseSize | celé číslo | Počet bajtů odeslaných do klienta během zpracování požadavku. | 
| cache | string | Stav zapojení mezipaměti služby API Management ve zpracování požadavku (tj. úspěšný přístup, neúspěšný přístup, žádné). | 
| cacheTime | celé číslo | Počet milisekund strávený na všech vstupně-výstupních operacích mezipaměti služby API Management (připojování, odesílání a příjem bajtů). | 
| backendTime | celé číslo | Počet milisekund strávený na všech vstupně-výstupních operacích back-endu (připojování, odesílání a příjem bajtů). | 
| clientTime | celé číslo | Počet milisekund strávený na všech vstupně-výstupních operacích klienta (připojování, odesílání a příjem bajtů). | 
| apiId | string | Identifikátor entity rozhraní API pro aktuální požadavek. | 
| operationId | string | Identifikátor entity operace pro aktuální požadavek. | 
| productId | string | Identifikátor entity produktu pro aktuální požadavek. | 
| userId | string | Identifikátor entity uživatele pro aktuální požadavek. | 
| apimSubscriptionId | string | Identifikátor entity předplatného pro aktuální požadavek. | 
| backendId | string | Identifikátor entity back-endu pro aktuální požadavek. | 
| LastError | object | Poslední chyba zpracování požadavku. | 
| elapsed | celé číslo | Počet milisekund uplynulých mezi okamžikem, kdy brána přijala požadavek, a okamžik, kdy došlo k chybě | 
| source | string | Název zásady nebo interní obslužné rutiny zpracování, která způsobila chybu. | 
| scope | string | Obor dokumentu zásad obsahující zásadu, která způsobila chybu. | 
| section | string | Část dokumentu zásad obsahující zásadu, která způsobila chybu. | 
| reason | string | Důvod chyby | 
| zpráva | string | Chybová zpráva | 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zobrazení protokolů aktivit
> * Zobrazit diagnostické protokoly
> * Zobrazit metriky rozhraní API
> * Nastavit pravidlo upozornění při neoprávněných voláních vašeho rozhraní API

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Trasování volání](api-management-howto-api-inspector.md)
