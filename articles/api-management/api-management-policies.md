---
title: Zásady správy rozhraní Azure API | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71219473"
---
# <a name="api-management-policies"></a>Zásady služby API Management
Tato část obsahuje odkaz na následující zásady správy rozhraní API. Informace o přidávání a konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](api-management-howto-policies.md).  
  
 Zásady jsou výkonné funkce systému, které umožňují vydavateli změnit chování rozhraní API prostřednictvím konfigurace. Zásady jsou kolekce příkazů, které jsou prováděny postupně na požadavek nebo odpověď rozhraní API. Mezi oblíbené příkazy patří převod formátu z XML na JSON a omezení rychlosti volání, která omezuje množství příchozích hovorů od vývojáře. Mnoho dalších zásad je k dispozici izokrabice.  
  
 Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, například [řízení toku](api-management-advanced-policies.md#choose) a [nastavená proměnná](api-management-advanced-policies.md#set-variable), jsou založené na výrazech zásad. Další informace najdete v článcích [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies) a [Výrazy zásad](api-management-policy-expressions.md).  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a> Zásady  
  
-   [Zásady omezení přístupu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Kontrola hlavičky HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) – vynucuje existenci nebo hodnotu hlavičky HTTP.  
    -   [Omezit rychlost volání podle předplatného](api-management-access-restriction-policies.md#LimitCallRate) – zabrání špičkám využití rozhraní API omezením rychlosti volání na základě předplatného.  
    -   [Omezit rychlost volání podle klíče](api-management-access-restriction-policies.md#LimitCallRateByKey) – zabrání špičkám využití rozhraní API omezením rychlosti volání na základě klíče.  
    -   [Omezit ip adresy volajícího](api-management-access-restriction-policies.md#RestrictCallerIPs) – filtry (povolí/odepře) volání z konkrétních IP adres a/nebo rozsahů adres.  
    -   [Nastavení kvóty využití podle předplatného](api-management-access-restriction-policies.md#SetUsageQuota) – umožňuje vynutit obnovitelný nebo dlouhodobý objem volání nebo kvótu šířky pásma na základě předplatného.  
    -   [Nastavení kvóty využití podle klíče](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – umožňuje vynutit obnovitelný nebo dlouhodobý objem volání nebo kvótu šířky pásma na základě klíče.  
    -   [Ověřit JWT](api-management-access-restriction-policies.md#ValidateJWT) - Vynucuje existenci a platnost JWT extrahované z zadané hlavičky HTTP nebo zadaný parametr dotazu.  
-   [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Tok řízení](api-management-advanced-policies.md#choose) - Podmíněně použije příkazy zásad na základě vyhodnocení logických výrazů.  
    -   [Předávání požadavků](api-management-advanced-policies.md#ForwardRequest) – předá požadavek back-endové službě.
    -   [Omezit souběžnost](api-management-advanced-policies.md#LimitConcurrency) – zabrání uzavřené zásady provádění o více než zadaný počet požadavků najednou.
    -   [Protokolovat do centra událostí](api-management-advanced-policies.md#log-to-eventhub) - odesílá zprávy v zadaném formátu do cíle zprávy definovaného entitou Logger.
    -   [Mock odpověď](api-management-advanced-policies.md#mock-response) - Přeruší spuštění kanálu a vrátí zesměšňované odpovědi přímo volajícímu.
    -   [Opakování](api-management-advanced-policies.md#Retry) - opakování spuštění uzavřených příkazů zásad, pokud a dokud není splněna podmínka. Spuštění se bude opakovat v určených časových intervalech a až do zadaného počtu opakování.  
    -   [Návratová odpověď](api-management-advanced-policies.md#ReturnResponse) - Přeruší spuštění kanálu a vrátí zadanou odpověď přímo volajícímu.  
    -   [Odeslat jednosměrný požadavek](api-management-advanced-policies.md#SendOneWayRequest) - Odešle požadavek na zadanou adresu URL bez čekání na odpověď.  
    -   [Odeslat požadavek](api-management-advanced-policies.md#SendRequest) - Odešle požadavek na zadanou adresu URL.
    -   [Nastavení http proxy -](api-management-advanced-policies.md#SetHttpProxy) Umožňuje směrovat předané požadavky prostřednictvím proxy serveru HTTP.
    -   [Set variable](api-management-advanced-policies.md#set-variable) - Persist a value in a named context variable for later access.  
    -   [Set request method](api-management-advanced-policies.md#SetRequestMethod) - Umožňuje změnit metodu HTTP pro požadavek.  
    -   [Nastavit stavový kód](api-management-advanced-policies.md#SetStatus) - Změní stavový kód HTTP na zadanou hodnotu.  
    -   [Trasování](api-management-advanced-policies.md#Trace) – přidá vlastní trasování do výstupu [nástroje API Inspector,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) telemetry Application Insights a diagnostické protokoly.  
    -   [Čekání](api-management-advanced-policies.md#Wait) - Čeká na přiložené [odeslat požadavek](api-management-advanced-policies.md#SendRequest), [Získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey)nebo [Řízení toku](api-management-advanced-policies.md#choose) zásady dokončit před pokračováním.  
-   [Zásady ověřování](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Ověření pomocí základní](api-management-authentication-policies.md#Basic) - ověření pomocí back-endové služby pomocí základního ověřování.  
    -   [Ověření pomocí klientského certifikátu](api-management-authentication-policies.md#ClientCertificate) – Ověření pomocí back-endové služby pomocí klientských certifikátů.  
    -   [Ověření pomocí spravované identity](api-management-authentication-policies.md#ManagedIdentity) – Ověření pomocí back-endové služby pomocí [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Zásady ukládání do mezipaměti](api-management-caching-policies.md#CachingPolicies)  
    -   [Získat z mezipaměti](api-management-caching-policies.md#GetFromCache) – Proveďte vyhledávání mezipaměti a vraťte platnou odpověď uloženou v mezipaměti, pokud je k dispozici.  
    -   [Uložit do mezipaměti](api-management-caching-policies.md#StoreToCache) - Mezipaměť odpověď podle zadané konfigurace ovládacího prvku mezipaměti.  
    -   [Získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey) – načtení položky uložené v mezipaměti pomocí klíče.  
    -   [Ukládat hodnotu do mezipaměti](api-management-caching-policies.md#StoreToCacheByKey) – položku do mezipaměti uložte podle klíče.  
    -   [Odebrat hodnotu z mezipaměti](api-management-caching-policies.md#RemoveCacheByKey) – Odebere položku v mezipaměti pomocí klíče.  
-   [Mezidoménové zásady](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Povolit volání mezi doménami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – zpřístupní rozhraní API z aplikací Adobe Flash a klientů založených na prohlížeči Microsoft Silverlight.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) – přidá podporu sdílení prostředků mezi zdroji (CORS) do operace nebo rozhraní API pro povolení volání mezi doménami z klientů založených na prohlížeči.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - Přidá JSON s podporou odsazení (JSONP) pro operaci nebo rozhraní API pro povolení volání mezi doménami z klientů založených na prohlížeči JavaScript.  
-   [Zásady transformace](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Převést JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Převede tělo požadavku nebo odpovědi z JSON na XML.  
    -   [Převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Převede tělo požadavku nebo odpovědi z XML na JSON.  
    -   [Najít a nahradit řetězec v těle](api-management-transformation-policies.md#Findandreplacestringinbody) - Najde podřetězec požadavku nebo odpovědi a nahradí jej jiným podřetězcem.  
    -   [Adresy URL masky v obsahu](api-management-transformation-policies.md#MaskURLSContent) – přepíše (masky) odkazy v těle odezvy tak, aby ukazovaly na ekvivalentní odkaz přes bránu.  
    -   [Set back-endové služby](api-management-transformation-policies.md#SetBackendService) - změní back-endovou službu pro příchozí požadavek.  
    -   [Set body](api-management-transformation-policies.md#SetBody) - Nastaví text zprávy pro příchozí a odchozí požadavky.  
    -   [Nastavit hlavičku HTTP](api-management-transformation-policies.md#SetHTTPheader) – přiřadí hodnotu existující odpovědi nebo záhlaví požadavku nebo přidá novou hlavičku odpovědi nebo požadavku.  
    -   [Nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) - Přidá, nahradí hodnotu nebo odstraní parametr řetězce dotazu požadavku.  
    -   [Přepsat adresu URL](api-management-transformation-policies.md#RewriteURL) – převede adresu URL požadavku z veřejného formuláře do formuláře očekávaného webovou službou.  
    -   [Transformace XML pomocí XSLT](api-management-transformation-policies.md#XSLTransform) - Použije transformaci XSL na XML v těle požadavku nebo odpovědi.  



## <a name="next-steps"></a>Další kroky
Další informace o práci se zásadami naleznete v tématu:

+ [Zásady ve správě rozhraní API](api-management-howto-policies.md)
+ [Transformovat api](transform-api.md)
+ [Ukázky zásad](policy-samples.md)   
