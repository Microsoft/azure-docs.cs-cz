---
title: Zásady služby Azure API Management | Microsoft Docs
description: Další informace o zásady, které jsou k dispozici pro použití v Azure API Management.
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
ms.openlocfilehash: f617abc164761b4839b188822585eaa4eb0aacd6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936891"
---
# <a name="api-management-policies"></a>Zásady služby API Management
Tato část obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](api-management-howto-policies.md).  
  
 Zásady jsou vynikající funkcí systému, která vydavatelům umožňuje měnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které jsou prováděny postupně v požadavku nebo odezvy z rozhraní API. Oblíbené příkazy patří převod formátu XML do formátu JSON a četnosti omezení omezit množství příchozích volání od vývojáře. Jsou dostupné ihned mnoho další zásady.  
  
 Výrazy zásad můžete použít jako hodnoty atributů nebo textové hodnoty v libovolných zásadách API Management (pokud zásady neurčí jinak). Některé zásady, například [řízení toku](api-management-advanced-policies.md#choose) a [nastavená proměnná](api-management-advanced-policies.md#set-variable), jsou založené na výrazech zásad. Další informace najdete v článcích [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies) a [Výrazy zásad](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> zásady  
  
-   [Zásady omezení přístupu](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Kontrola HTTP záhlaví](api-management-access-restriction-policies.md#CheckHTTPHeader) -vynucuje existence nebo hodnoty hlavičky protokolu HTTP.  
    -   [Omezení četnosti volání podle předplatného](api-management-access-restriction-policies.md#LimitCallRate) -špičky využití brání rozhraní API omezením četnosti volání, na základě za předplatné.  
    -   [Omezení četnosti volání podle klíče](api-management-access-restriction-policies.md#LimitCallRateByKey) -špičky využití brání rozhraní API omezením četnosti volání, na základě na klíč.  
    -   [Omezit volající IP adresy](api-management-access-restriction-policies.md#RestrictCallerIPs) -filtry (umožňuje nebo zakazuje) volání z konkrétní IP adresy a rozsahy adres.  
    -   [Nastavení kvóty využití podle předplatného](api-management-access-restriction-policies.md#SetUsageQuota) -umožňuje vynucovat obnovitelných nebo doba života volání svazku nebo šířky pásma kvótu, na základě za předplatné.  
    -   [Nastavení kvóty využití podle klíče](api-management-access-restriction-policies.md#SetUsageQuotaByKey) -umožňuje vynucovat obnovitelných nebo doba života volání svazku nebo šířky pásma kvótu, na základě na klíč.  
    -   [Ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) -vynucuje existence a platnosti token JWT extrahovány ze zadaného záhlaví HTTP nebo parametr zadaný dotaz.  
-   [Pokročilé zásady](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Řízení toku](api-management-advanced-policies.md#choose) – podmíněně platí příkazy zásad na základě vyhodnocení výrazy logických hodnot.  
    -   [Předat dál žádost](api-management-advanced-policies.md#ForwardRequest) -předá požadavek back-end službu.  
    -   [Protokol do centra událostí](api-management-advanced-policies.md#log-to-eventhub) -odešle zprávy v zadaném formátu do zprávy Cíl definované entity protokolovacího nástroje.  
    -   [Opakujte](api-management-advanced-policies.md#Retry) -opakování provádění příkazů závorkách zásad, pokud a dokud nebude splněna podmínka. Spuštění se opakovaly zadaným časovým intervalům a až zadaný počet opakování.  
    -   [Vrátí odpověď](api-management-advanced-policies.md#ReturnResponse) -přerušení způsobených kanálu provádění a vrátí zadanou odpověď přímo na volajícího.  
    -   [Odeslání žádosti o jednorázové jednoduché](api-management-advanced-policies.md#SendOneWayRequest) -odešle požadavek na zadanou adresu URL bez čekání na odpověď.  
    -   [Odeslání požadavku](api-management-advanced-policies.md#SendRequest) -odešle požadavek na zadanou adresu URL.  
    -   [Nastavená proměnná](api-management-advanced-policies.md#set-variable) -zachovat hodnotu do proměnné s názvem kontext pro pozdější přístup.  
    -   [Nastaví metodu požadavku](api-management-advanced-policies.md#SetRequestMethod) -vám umožní změnit metodu protokolu HTTP pro žádost.  
    -   [Nastavit stavový kód](api-management-advanced-policies.md#SetStatus) -změní stavový kód protokolu HTTP se zadanou hodnotou.  
    -   [Trasování](api-management-advanced-policies.md#Trace) -přidá řetězec do [rozhraní API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) výstup.  
    -   [Počkejte](api-management-advanced-policies.md#Wait) -čeká pro uzavřené [odeslán požadavek na](api-management-advanced-policies.md#SendRequest), [získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey), nebo [řízení toku](api-management-advanced-policies.md#choose) zásady k dokončení než budete pokračovat.  
-   [Zásady ověřování](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Ověřování Basic](api-management-authentication-policies.md#Basic) -ověření pomocí back-end službu pomocí základního ověřování.  
    -   [Ověřování pomocí certifikátu klienta](api-management-authentication-policies.md#ClientCertificate) -ověření pomocí back-end službu pomocí klientských certifikátů.  
-   [Zásady ukládání do mezipaměti](api-management-caching-policies.md#CachingPolicies)  
    -   [Získat z mezipaměti](api-management-caching-policies.md#GetFromCache) -provést mezipaměti vyhledat a vrátit platnou odpověď uložená v mezipaměti pokud je k dispozici.  
    -   [Úložiště pro mezipaměť](api-management-caching-policies.md#StoreToCache) -ukládá do mezipaměti odpovědi podle konfigurace zadané mezipaměti ovládacího prvku.  
    -   [Získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey) -načíst položky v mezipaměti podle klíče.  
    -   [Uložení v mezipaměti hodnoty](api-management-caching-policies.md#StoreToCacheByKey) -uložit položky do mezipaměti podle klíče.  
    -   [Odebrat hodnotu z mezipaměti](api-management-caching-policies.md#RemoveCacheByKey) -odebrání položky v mezipaměti podle klíče.  
-   [Mezidoménové zásady](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Povolit volání mezi doménami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -zpřístupní rozhraní API z Adobe Flash a Microsoft Silverlight založené na prohlížeči klientů.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) – přidává podporu (CORS), aby operace nebo rozhraní API umožňující volání napříč doménami založené na prohlížeči klientů pro sdílení prostředků různého původu.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -přidá JSON s podporou odsazení (JSONP) operace nebo rozhraní API, chcete-li povolit volání mezi doménami z klientů JavaScript založené na prohlížeči.  
-   [Zásady transformace](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Převést JSON do XML](api-management-transformation-policies.md#ConvertJSONtoXML) – převede požadavku nebo odpovědi textu z JSON do XML.  
    -   [Převést na JSON XML](api-management-transformation-policies.md#ConvertXMLtoJSON) – převede požadavku nebo odpovědi textu z XML do formátu JSON.  
    -   [Najít a nahradit řetězec v textu](api-management-transformation-policies.md#Findandreplacestringinbody) – najde dílčí řetězec požadavku nebo odpovědi a nahradí je jiný dílčí řetězec.  
    -   [Maskování adresy URL v obsahu](api-management-transformation-policies.md#MaskURLSContent) -znovu zapíše (masky) odkazy v odpovědi body tak, aby ukazovaly na ekvivalentní propojení prostřednictvím brány.  
    -   [Nastavení back-end služby](api-management-transformation-policies.md#SetBackendService) -změny službě back-end pro příchozí žádosti.  
    -   [Tělo nastavit](api-management-transformation-policies.md#SetBody) -nastaví obsah zprávy pro příchozí a odchozí požadavky.  
    -   [Set – hlavička protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) – přiřazuje hodnotu existující odpovědi nebo hlavička požadavku nebo přidá hlavičku odpovědi nebo žádost o nový.  
    -   [Nastavte parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) – přidá, nahradí hodnotu nebo odstraní parametr řetězce dotazu požadavku.  
    -   [Přepsání adresy URL](api-management-transformation-policies.md#RewriteURL) – převede adresu URL požadavku z jeho veřejné formuláře do formuláře očekává webovou službou.  
    -   [Transformace XML pomocí transformaci XSLT](api-management-transformation-policies.md#XSLTransform) -použije transformaci XSL na XML v textu požadavku nebo odpovědi.  



## <a name="next-steps"></a>Další postup
Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
