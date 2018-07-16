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
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: b4c885758f572851f058edb6e7851d650faed9f9
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972994"
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
>
>

## <a name="prerequisites"></a>Požadavky

+ Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="view-metrics-of-your-apis"></a>Zobrazení metrik vašich rozhraní API

API Management každou minutu vysílá metriky, takže vám skoro v reálném čase poskytuje přehled o stavu vašich rozhraní API. Toto je souhrn některých dostupných metrik:

* Kapacita (verze Preview): pomáhá při rozhodování o přechodu na vyšší nebo nižší verzi služeb APIM. Metrika se generuje každou minutu a odráží kapacitu brány v čase vytvoření sestavy. Její hodnoty se pohybují v rozsahu od 0 do 100 a počítají se na základě prostředků brány, jako je využití procesoru nebo paměti.
* Celkový počet požadavků brány: počet požadavků rozhraní API za dané období. 
* Úspěšné požadavky brány: počet požadavků rozhraní API, které obdržely kódy úspěšné odpovědi HTTP včetně kódů 304, 307 a veškerých kódů menších než 301 (například 200).
* Neúspěšné požadavky brány: počet požadavků rozhraní API, které obdržely kódy chybné odpovědi HTTP včetně kódů 400 a veškerých kódů větších než 500.
* Neoprávněné požadavky brány: počet požadavků rozhraní API, které obdržely kódy odpovědi HTTP jako 401, 403 a 429.
* Ostatní požadavky brány: počet požadavků rozhraní API, které obdržely kódy odpovědi HTTP nepatřící do žádné z výše uvedených skupin (například 418).

Přístup k metrikám:

1. V nabídce ve spodní části stránky vyberte **Metriky**.

    ![Průzkumníku metrik](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. V rozevíracím seznamu vyberte metriky, které vás zajímají (můžete jich přidat několik).  
    V seznamu dostupných metrik vyberte třeba **Celkový počet požadavků brány** a **Neúspěšné požadavky brány**.
3. Graf zobrazí celkový počet volání rozhraní API. Ukazuje také počet volání rozhraní API, která se nezdařila.

    ![Graf metrik](./media/api-management-azure-monitor/apim-monitor-metrics.png)

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Nastavení pravidla upozornění při neoprávněných požadavcích

Můžete nakonfigurovat odesílání upozornění na základě metrik a protokolů aktivit. Azure Monitor umožňuje nakonfigurovat upozornění, které při aktivaci provede některé z následujících kroků:

* Odeslání e-mailového oznámení
* Volání webhooku
* Vyvolání aplikace logiky Azure

Konfigurace upozornění:

1. V řádku nabídek ve spodní části stránky vyberte **Upozornění (klasická)**.

    ![výstrahy](./media/api-management-azure-monitor/api-management-alert-rules-blade.png)

2. Vyberte **Přidat upozornění metriky**.
3. Zadejte **Název** tohoto upozornění.
4. Jako metriku k monitorování vyberte **Neoprávněné požadavky brány**.
5. Vyberte možnost **Vlastníci, přispěvatelé a čtenáři e-mailů**.
6. Stiskněte **OK**.
7. Zkuste volat rozhraní Conference API bez klíče rozhraní API. Jako vlastník této služby API Management dostanete e-mailem upozornění. 

    > [!TIP]
    > Pravidlo upozornění může také při aktivaci volat webhook nebo aplikaci logiky Azure.

    ![Nastavení upozornění](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="activity-logs"></a>Protokoly aktivit

Protokoly aktivit poskytují přehled o operacích provedených vašimi službami API Management. Pomocí protokolů aktivit můžete zjistit obsah, autora a čas veškerých operací zápisu (PUT, POST, DELETE) provedených ve vašich službách API Management.

> [!NOTE]
> Protokoly aktivit nezahrnují operace čtení (GET) ani operace prováděné na webu Azure Portal nebo pomocí původních rozhraní API pro správu.

Protokoly aktivit můžete zobrazit ve své službě API Management nebo k nim můžete získat přístup ze všech svých prostředků Azure prostřednictvím služby Azure Monitor. 

Zobrazení protokolů aktivit:

1. Vyberte instanci služby APIM.
2. Klikněte na **Protokol aktivit**.

    ![Protokol aktivit](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Vyberte požadovaný obor filtrování a klikněte na **Použít**.

    ![Protokoly aktivit](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Diagnostické protokoly poskytují spoustu informací o operacích a chybách, které jsou důležité pro audit i pro účely odstraňování potíží. Diagnostické protokoly se liší od protokolů aktivit. Protokoly aktivit poskytují přehled o operacích provedených ve vašich prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které provedly vaše prostředky.

Konfigurace diagnostických protokolů:

1. Vyberte instanci služby APIM.
2. Klikněte na **Diagnostické protokoly**.

    ![Diagnostické protokoly](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Klikněte na **Zapnout diagnostiku**. Diagnostické protokoly můžete společně s metrikami archivovat v účtu úložiště, streamovat je do centra událostí nebo je odesílat do Log Analytics. 

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
| time | date-time | Časové razítko přijetí požadavku HTTP v bráně. |
| operationName | řetězec | Konstantní hodnota Microsoft.ApiManagement/GatewayLogs. |
| category | řetězec | Konstantní hodnota GatewayLogs. |
| durationMs | celé číslo | Počet milisekund od chvíle, kdy brána přijala požadavek, do chvíle odeslání úplné odpovědi. |
| callerIpAddress | řetězec | IP adresa bezprostředního volajícího brány (může být prostředníkem). |
| correlationId | řetězec | Jedinečný identifikátor požadavku HTTP přiřazený službou API Management. |
| location | řetězec | Název oblasti Azure, ve které se nachází brána, která požadavek zpracovala. |
| httpStatusCodeCategory | řetězec | Kategorie stavového kódu odpovědi HTTP: Úspěch (301 nebo nižší, 304 nebo 307), Neautorizováno (401, 403, 429), Chyba (400, 500 až 600), Jiné. |
| resourceId | řetězec | ID prostředku služby API Management /SUBSCRIPTIONS/<subscription>/RESOURCEGROUPS/<skupina_prostředků>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/<name> |
| properties | objekt | Vlastnosti aktuálního požadavku. |
| method | řetězec | Metoda HTTP příchozího požadavku. |
| url | řetězec | Adresa URL příchozího požadavku. |
| clientProtocol | řetězec | Verze protokolu HTTP příchozího požadavku. |
| responseCode | celé číslo | Stavový kód odpovědi HTTP odeslané do klienta. |
| backendMethod | řetězec | Metoda HTTP požadavku odeslaného do back-endu. |
| backendUrl | řetězec | Adresa URL požadavku odeslaného do back-endu. |
| backendResponseCode | celé číslo | Kód odpovědi HTTP přijaté z back-endu. |
| backendProtocol | řetězec | Verze protokolu HTTP požadavku odeslaného do back-endu. | 
| requestSize | celé číslo | Počet bajtů přijatých z klienta během zpracování požadavku. | 
| responseSize | celé číslo | Počet bajtů odeslaných do klienta během zpracování požadavku. | 
| cache | řetězec | Stav zapojení mezipaměti služby API Management ve zpracování požadavku (tj. úspěšný přístup, neúspěšný přístup, žádné). | 
| cacheTime | celé číslo | Počet milisekund strávený na všech vstupně-výstupních operacích mezipaměti služby API Management (připojování, odesílání a příjem bajtů). | 
| backendTime | celé číslo | Počet milisekund strávený na všech vstupně-výstupních operacích back-endu (připojování, odesílání a příjem bajtů). | 
| clientTime | celé číslo | Počet milisekund strávený na všech vstupně-výstupních operacích klienta (připojování, odesílání a příjem bajtů). | 
| apiId | řetězec | Identifikátor entity rozhraní API pro aktuální požadavek. | 
| operationId | řetězec | Identifikátor entity operace pro aktuální požadavek. | 
| productId | řetězec | Identifikátor entity produktu pro aktuální požadavek. | 
| userId | řetězec | Identifikátor entity uživatele pro aktuální požadavek. | 
| apimSubscriptionId | řetězec | Identifikátor entity předplatného pro aktuální požadavek. | 
| backendId | řetězec | Identifikátor entity back-endu pro aktuální požadavek. | 
| LastError | objekt | Poslední chyba zpracování požadavku. | 
| elapsed | celé číslo | Počet milisekund, které uplynuly od přijetí požadavku bránou do chvíle, kdy došlo k chybě. | 
| source | řetězec | Název zásady nebo interní obslužné rutiny zpracování, která způsobila chybu. | 
| scope | řetězec | Obor dokumentu zásad obsahující zásadu, která způsobila chybu. | 
| section | řetězec | Část dokumentu zásad obsahující zásadu, která způsobila chybu. | 
| reason | řetězec | Důvod chyby | 
| zpráva | řetězec | Chybová zpráva | 

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
