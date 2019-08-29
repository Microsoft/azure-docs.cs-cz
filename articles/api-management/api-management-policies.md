---
title: Zásady Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách, které jsou k dispozici pro použití v Azure API Managementu.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: ed20ef023a884ac79150cd31f25fde86b715c752
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073396"
---
# <a name="api-management-policies"></a>Zásady služby API Management
V této části najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](api-management-howto-policies.md).  
  
 Zásady představují výkonnou schopnost systému, která umožňuje vydavateli změnit chování rozhraní API prostřednictvím konfigurace. Zásady jsou kolekce příkazů, které jsou spouštěny postupně na žádost nebo na reakci rozhraní API. Mezi oblíbené příkazy patří převod formátu z XML na JSON a omezení rychlosti volání, aby se omezilo množství příchozích volání od vývojáře. Mnoho dalších zásad je dostupných mimo box.  
  
 Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, například [řízení toku](api-management-advanced-policies.md#choose) a [nastavená proměnná](api-management-advanced-policies.md#set-variable), jsou založené na výrazech zásad. Další informace najdete v článcích [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies) a [Výrazy zásad](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a>Konfigurovaný  
  
-   [Zásady omezení přístupu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Zkontroluje HLAVIČKU http](api-management-access-restriction-policies.md#CheckHTTPHeader) – vynutila existenci nebo hodnotu hlavičky HTTP.  
    -   [Omezení četnosti volání podle](api-management-access-restriction-policies.md#LimitCallRate) předplatného – zabrání špičkám využití rozhraní API omezením četnosti volání na základě jednotlivých předplatných.  
    -   [Omezení četnosti volání podle klíče](api-management-access-restriction-policies.md#LimitCallRateByKey) – zabrání špičkám využití rozhraní API omezením četnosti volání, a to za klíčovým základem.  
    -   [Omezení](api-management-access-restriction-policies.md#RestrictCallerIPs) počtu IP adres volajících – filtry (povolující a zakazují) volání z konkrétních IP adres nebo rozsahů adres.  
    -   [Nastavení kvóty využití podle](api-management-access-restriction-policies.md#SetUsageQuota) předplatného – umožňuje vynutilit obnovitelné nebo maximální objem volání nebo kvótu šířky pásma, a to na základě jednotlivých předplatných.  
    -   [Nastavení kvóty využití podle klíče](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – umožňuje vynutilit obnovitelné nebo maximální objem volání nebo kvótu šířky pásma, a to na základě jednotlivých klíčů.  
    -   [Ověří](api-management-access-restriction-policies.md#ValidateJWT) , zda existence tokenu JWT vynutila platnost a zda byla extrahována z buď zadaného záhlaví protokolu HTTP, nebo zadaného parametru dotazu.  
-   [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Tok řízení](api-management-advanced-policies.md#choose) – podmíněně aplikuje příkazy zásad na základě vyhodnocení logických výrazů.  
    -   [Dopředné žádosti](api-management-advanced-policies.md#ForwardRequest) – přepošle požadavek do služby back-end.
    -   [Limit Concurrency](api-management-advanced-policies.md#LimitConcurrency) – zabrání v současné době spouštění uzavřených zásad ve více než zadaném počtu požadavků najednou.
    -   [Protokol do centra událostí](api-management-advanced-policies.md#log-to-eventhub) – posílá zprávy v zadaném formátu do cíle zprávy, který je definovaný entitou protokolovacího nástroje.
    -   [Přípravou odezva](api-management-advanced-policies.md#mock-response) – přeruší spuštění kanálu a vrátí napodobnou odpověď přímo volajícímu.
    -   [Opakovat](api-management-advanced-policies.md#Retry) – pokusy o provedení uzavřených příkazů zásad, pokud a dokud podmínka není splněna. Spuštění se zopakuje v zadaných časových intervalech a až do zadaného počtu opakování.  
    -   [Návratová odpověď](api-management-advanced-policies.md#ReturnResponse) – přeruší spuštění kanálu a vrátí zadanou odpověď přímo volajícímu.  
    -   [Odeslání](api-management-advanced-policies.md#SendOneWayRequest) jednosměrné žádosti – pošle požadavek na zadanou adresu URL bez čekání na odpověď.  
    -   [Odeslat požadavek](api-management-advanced-policies.md#SendRequest) – pošle požadavek na zadanou adresu URL.
    -   [Nastavení proxy serveru http](api-management-advanced-policies.md#SetHttpProxy) – umožňuje směrovat přesměrované žádosti přes proxy HTTP.
    -   [Nastavte proměnnou](api-management-advanced-policies.md#set-variable) – zachovat hodnotu v pojmenované kontextové proměnné pro pozdější přístup.  
    -   [Nastavit metodu žádosti](api-management-advanced-policies.md#SetRequestMethod) – umožňuje změnit metodu HTTP pro požadavek.  
    -   [Nastavit stavový kód](api-management-advanced-policies.md#SetStatus) – změní stavový kód HTTP na zadanou hodnotu.  
    -   [Trace](api-management-advanced-policies.md#Trace) – přidá řetězec do výstupu [inspektoru rozhraní API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) .  
    -   [Wait](api-management-advanced-policies.md#Wait) -čeká na uzavřenou [žádost o odeslání](api-management-advanced-policies.md#SendRequest), před pokračováním [Získá hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey)nebo zásady [toku řízení](api-management-advanced-policies.md#choose) .  
-   [Zásady ověřování](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [](api-management-authentication-policies.md#Basic) Ověřování pomocí služby back-end s využitím základního ověřování pomocí back-endu  
    -   [Ověřování pomocí klientského certifikátu](api-management-authentication-policies.md#ClientCertificate) – ověřování pomocí back-end služby pomocí klientských certifikátů.  
    -   [Ověřování pomocí spravované identity](api-management-authentication-policies.md#ManagedIdentity) – ověřování pomocí back-end služby pomocí [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Zásady ukládání do mezipaměti](api-management-caching-policies.md#CachingPolicies)  
    -   [Získat z mezipaměti](api-management-caching-policies.md#GetFromCache) – vyhledá mezipaměť a vrátí platnou odpověď uloženou v mezipaměti, pokud je dostupná.  
    -   [Ukládat do](api-management-caching-policies.md#StoreToCache) mezipaměti cache – odpověď v závislosti na zadané konfiguraci řízení mezipaměti.  
    -   [Získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey) – načte položku uloženou v mezipaměti podle klíče.  
    -   [Uložit hodnotu v mezipaměti](api-management-caching-policies.md#StoreToCacheByKey) – uložit položku do mezipaměti podle klíče  
    -   [Odebrat hodnotu z mezipaměti](api-management-caching-policies.md#RemoveCacheByKey) – odebere položku v mezipaměti podle klíče.  
-   [Mezidoménové zásady](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Povolení mezidoménových volání](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – ZPŘÍSTUPŇUJE rozhraní API z klientů Adobe Flash a prohlížeče založeného na prohlížeči Microsoft Silverlight.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) – přidává podporu pro sdílení prostředků mezi zdroji (CORS) pro operaci nebo rozhraní API, která umožňuje mezidoménová volání z klientů založených na prohlížeči.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) – přidá do operace nebo do rozhraní API podporu formátu JSON s doplňováním (JSONP), která umožňuje mezidoménová volání z klientů v prohlížeči JavaScript.  
-   [Zásady transformace](api-management-transformation-policies.md#TransformationPolicies)  
    -   Převede [JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) – převede požadavek nebo tělo odpovědi z JSON na XML.  
    -   [Převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – převede text žádosti nebo odpovědi z XML na JSON.  
    -   [Najít a nahradit řetězec v těle](api-management-transformation-policies.md#Findandreplacestringinbody) – najde požadavek nebo podřetězec odpovědi a nahradí ho jiným podřetězcem.  
    -   [Adresy URL masek v](api-management-transformation-policies.md#MaskURLSContent) odkazech Content-repisujecích (Masks) v těle odpovědi tak, aby odkazovaly na ekvivalentní odkaz přes bránu.  
    -   [Nastavit back-end službu](api-management-transformation-policies.md#SetBackendService) – změní back-end službu pro příchozí požadavek.  
    -   [Nastavit tělo](api-management-transformation-policies.md#SetBody) – nastaví tělo zprávy pro příchozí a odchozí požadavky.  
    -   [Nastavit HLAVIČKU http](api-management-transformation-policies.md#SetHTTPheader) – přiřadí hodnotu existující odpovědi nebo hlavičce požadavku nebo přidá novou odpověď nebo hlavičku požadavku.  
    -   [Nastavte parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) -přidat, nahradí hodnotu nebo odstraní parametr řetězce dotazu Request.  
    -   [Adresa URL pro přepsání](api-management-transformation-policies.md#RewriteURL) – převede adresu URL požadavku z veřejného formuláře na formulář, který tato webová služba očekává.  
    -   [Transformuje XML pomocí XSLT](api-management-transformation-policies.md#XSLTransform) – aplikuje transformaci XSL na XML v těle žádosti nebo odpovědi.  



## <a name="next-steps"></a>Další kroky
Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Ukázky zásad](policy-samples.md)   
