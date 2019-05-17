---
title: Zásady služby Azure API Management | Dokumentace Microsoftu
description: Přečtěte si o zásadách, které jsou k dispozici pro použití v Azure API Managementu.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: ad57f86e221a98461fcd1dc11b92f5665d6128d0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786451"
---
# <a name="api-management-policies"></a>Zásady služby API Management
Tato část obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](api-management-howto-policies.md).  
  
 Zásady jsou vynikající funkcí systému, která vydavatelům umožňuje měnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které jsou spouštěny postupně na požadavku nebo odezvy z rozhraní API. Oblíbené příkazy patří převod formátu XML do formátu JSON a četnosti omezení omezit množství příchozích volání od vývojáře. Mnoho více zásad jsou dostupné ihned.  
  
 Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, například [řízení toku](api-management-advanced-policies.md#choose) a [nastavená proměnná](api-management-advanced-policies.md#set-variable), jsou založené na výrazech zásad. Další informace najdete v článcích [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies) a [Výrazy zásad](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> Zásady  
  
-   [Zásady omezení přístupu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Kontrola protokolu HTTP hlavičce](api-management-access-restriction-policies.md#CheckHTTPHeader) -vynucuje existence a/nebo hodnotu hlavičky protokolu HTTP.  
    -   [Omezení četnosti volání podle předplatného](api-management-access-restriction-policies.md#LimitCallRate) -špičky využití brání rozhraní API omezením četnosti volání podle předplatného.  
    -   [Omezení četnosti volání podle klíče](api-management-access-restriction-policies.md#LimitCallRateByKey) -špičky využití brání rozhraní API omezením četnosti volání, na základě za klíč.  
    -   [Omezení IP adresy volajícího](api-management-access-restriction-policies.md#RestrictCallerIPs) – filtry (umožňuje nebo zakazuje) volání z konkrétních IP adres a rozsahy adres.  
    -   [Nastavení kvóty využití podle předplatného](api-management-access-restriction-policies.md#SetUsageQuota) – slouží k vynucování obnovitelných nebo doba života volání svazku a/nebo šířku pásma kvóty, na základě předplatného.  
    -   [Nastavení kvóty využití podle klíče](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – slouží k vynucování obnovitelných nebo doba života volání svazku a/nebo šířku pásma kvóty, na základě za klíč.  
    -   [Ověřit token JWT](api-management-access-restriction-policies.md#ValidateJWT) -vynucuje existenci a platnost extrahují z zadanou hlavičku protokolu HTTP nebo parametr dotazu zadaný token JWT.  
-   [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Řízení toku](api-management-advanced-policies.md#choose) – podmíněné příkazy zásad na základě vyhodnocení logické výrazy platí.  
    -   [Dopředné požadavek](api-management-advanced-policies.md#ForwardRequest) -předá požadavek back-end službu.  
    -   [Protokolování do Eventhubu](api-management-advanced-policies.md#log-to-eventhub) -odesílá zprávy v zadaném formátu do cíl zprávy určené entity protokolovacího nástroje.  
    -   [Zkuste](api-management-advanced-policies.md#Retry) -opakuje spouštění příkazů uzavřených zásady, pokud a dokud je podmínka splněna. Spuštění se opakovaly s zadaným časovým intervalům a až po zadaný počet opakování.  
    -   [Vrací odpověď](api-management-advanced-policies.md#ReturnResponse) -kanálu přeruší provádění a vrátí zadanou odpověď přímo na volajícího.  
    -   [Odeslat jednosměrný požadavek](api-management-advanced-policies.md#SendOneWayRequest) – odešle požadavek na zadanou adresu URL bez čekání na odpověď.  
    -   [Poslat žádost o](api-management-advanced-policies.md#SendRequest) – odešle požadavek na zadanou adresu URL.  
    -   [Nastavit proměnnou](api-management-advanced-policies.md#set-variable) – zachovat hodnotu do proměnné pojmenovaného kontextu pro pozdější přístup.  
    -   [Nastaví metodu požadavku](api-management-advanced-policies.md#SetRequestMethod) -vám umožní změnit metodu HTTP pro žádost.  
    -   [Nastavené kódy stavu](api-management-advanced-policies.md#SetStatus) – změní stavový kód HTTP se zadanou hodnotou.  
    -   [Trasování](api-management-advanced-policies.md#Trace) – přidá řetězec do [pro inspekci API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) výstup.  
    -   [Počkejte](api-management-advanced-policies.md#Wait) – čeká uzavřen [požadavků na odeslání](api-management-advanced-policies.md#SendRequest), [získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey), nebo [řízení toku](api-management-advanced-policies.md#choose) zásady než budete pokračovat, dokončete.  
-   [Zásady ověřování](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Ověřování pomocí Basic](api-management-authentication-policies.md#Basic) – ověřování pomocí back-end službu pomocí základního ověřování.  
    -   [Ověřování pomocí certifikátu klienta](api-management-authentication-policies.md#ClientCertificate) – ověřování pomocí služby back-end pomocí klientských certifikátů.  
    -   [Ověřování pomocí spravované identity](api-management-authentication-policies.md#ManagedIdentity) – ověřování pomocí služby back-endu [se identita spravované](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Zásady ukládání do mezipaměti](api-management-caching-policies.md#CachingPolicies)  
    -   [Získat z mezipaměti](api-management-caching-policies.md#GetFromCache) -provádět mezipaměti vyhledat a vracet platnou odpověď uložená v mezipaměti-li k dispozici.  
    -   [Store do mezipaměti](api-management-caching-policies.md#StoreToCache) -ukládá do mezipaměti odpovědi závislosti na konfiguraci mezipaměti zadaný ovládací prvek.  
    -   [Získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey) – načtení položky v mezipaměti podle klíče.  
    -   [Store hodnota v mezipaměti](api-management-caching-policies.md#StoreToCacheByKey) -Store položku v mezipaměti podle klíče.  
    -   [Odebrat hodnotu z mezipaměti](api-management-caching-policies.md#RemoveCacheByKey) -odebrat položku v mezipaměti podle klíče.  
-   [Mezidoménové zásady](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Povolit volání mezi doménami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -zpřístupňuje rozhraní API z Adobe Flash a Silverlight Microsoftu založené na prohlížeči klientů.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) – přidá prostředků mezi zdroji (CORS) podporu o operaci nebo rozhraní API pro povolit volání mezi doménami založené na prohlížeči klientů pro sdílení obsahu.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) – přidá JSON s podporou odsazení (JSONP) pro operaci nebo rozhraní API pro volání mezi doménami založené na prohlížeči klientů JavaScript povolit.  
-   [Zásady transformace](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Převést JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) – převede požadavku nebo odpovědi zprávy z formátu JSON, který se XML.  
    -   [Převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – převede požadavku nebo odpovědi text ze souboru XML na JSON.  
    -   [Najít a nahradit řetězec v těle](api-management-transformation-policies.md#Findandreplacestringinbody) – najde dílčí řetězec požadavku nebo odpovědi a nahradí jiný dílčí řetězec.  
    -   [Maskovat adresy URL v obsahu](api-management-transformation-policies.md#MaskURLSContent) – přepíše (masky) odkazy v odpovědi body tak, aby ukazovaly na ekvivalentní propojení prostřednictvím brány.  
    -   [Nastavte Back-endová služba](api-management-transformation-policies.md#SetBackendService) – změní back-end službu pro příchozí požadavek.  
    -   [Nastavit text](api-management-transformation-policies.md#SetBody) – nastaví text zprávy pro příchozí a odchozí požadavky.  
    -   [Set – hlavička protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) – přiřadí hodnotu existující odpověď a/nebo hlavička požadavku nebo přidá hlavičku odpovědi a/nebo žádost o nový.  
    -   [Nastavte parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) – přidá, nahradí hodnotu nebo odstraní parametru řetězce dotazu požadavku.  
    -   [Přepisu adresy URL](api-management-transformation-policies.md#RewriteURL) – převede adrese URL žádosti z jeho veřejné formuláře do formuláře, byl očekáván webová služba.  
    -   [Transformace XML pomocí XSLT](api-management-transformation-policies.md#XSLTransform) -transformace XSL se vztahuje na XML v textu požadavku nebo odpovědi.  



## <a name="next-steps"></a>Další postup
Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Ukázky zásad](policy-samples.md)   
