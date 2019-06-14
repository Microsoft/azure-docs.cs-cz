---
title: Referenční model dat šablony služby Azure API Management | Dokumentace Microsoftu
description: Další informace o entitu a typ záruky pro společné položky, které se používá v datových modelů pro šablon portálu pro vývojáře ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 3c2384b536235554fed7c1cf1a08b7c665f513a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61094518"
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management šablony data referenční informace k modelu
Toto téma popisuje entitu a typ záruky pro společné položky, které se používá v datových modelů pro šablon portálu pro vývojáře ve službě Azure API Management.  
  
 Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

Portál pro vývojáře není k dispozici na úrovni Consumption.

## <a name="reference"></a>Referenční informace

-   [Rozhraní API](#API)  
-   [Souhrn rozhraní API](#APISummary)  
-   [Aplikace](#Application)  
-   [Přílohy](#Attachment)  
-   [Ukázka kódu](#Sample)  
-   [Komentář](#Comment)  
-   [Filtrování](#Filtering)  
-   [Header](#Header)  
-   [Požadavek HTTP](#HTTPRequest)  
-   [Odpověď HTTP](#HTTPResponse)  
-   [Problém](#Issue)  
-   [Operace](#Operation)  
-   [Operace nabídky](#Menu)  
-   [Položka nabídky operace](#MenuItem)  
-   [Stránkování](#Paging)  
-   [Parametr](#Parameter)  
-   [Produkt](#Product)  
-   [Poskytovatel](#Provider)  
-   [Reprezentace](#Representation)  
-   [Předplatné](#Subscription)  
-   [Souhrn předplatného](#SubscriptionSummary)  
-   [Informace o uživatelském účtu](#UserAccountInfo)  
-   [Přihlášení uživatele](#UseSignIn)  
-   [Registrace uživatele](#UserSignUp)  
  
##  <a name="API"></a> API  
 `API` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|string|Identifikátor URI. Jednoznačně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}` kde `{id}` je identifikátor rozhraní API. Tato vlastnost je jen pro čtení.|  
|`name`|string|Název rozhraní API. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|string|Popis rozhraní API. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální počet znaků je 1000 znaků.|  
|`serviceUrl`|string|Absolutní adresa URL back-end službu implementující toto rozhraní API.|  
|`path`|string|Relativní adresa URL jednoznačně určující toto rozhraní API a všechny jeho cesty prostředků v rámci instance služby API Management. Se připojí k rozhraní API koncového bodu základní adresu URL zadané při vytváření instance služby tvoří veřejnou adresu URL pro toto rozhraní API.|  
|`protocols`|pole čísla|Popisuje na protokoly, které lze vyvolat operace v tomto rozhraní API. Povolené hodnoty jsou `1 - http` a `2 - https`, nebo obojí.|  
|`authenticationSettings`|[Nastavení ověřování serveru ověřování](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekce nastavení ověřování, které jsou součástí tohoto rozhraní API.|  
|`subscriptionKeyParameterNames`|objekt|Volitelná vlastnost, která slouží k určení vlastní názvy parametrů dotazu nebo záhlaví obsahující klíč předplatného. Když tato vlastnost je k dispozici, musí obsahovat alespoň jeden z následujících dvou vlastností.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Souhrn rozhraní API  
 `API summary` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|string|Identifikátor URI. Jednoznačně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}` kde `{id}` je identifikátor rozhraní API. Tato vlastnost je jen pro čtení.|  
|`name`|string|Název rozhraní API. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|string|Popis rozhraní API. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální počet znaků je 1000 znaků.|  
  
##  <a name="Application"></a> Aplikace  
 `application` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Jedinečný identifikátor aplikace.|  
|`Title`|string|Název aplikace.|  
|`Description`|string|Popis aplikace.|  
|`Url`|Identifikátor URI|Identifikátor URI pro aplikaci.|  
|`Version`|string|Informace o verzi pro aplikaci.|  
|`Requirements`|string|Popis požadavků pro aplikaci.|  
|`State`|číslo|Aktuální stav aplikace.<br /><br /> -0 - zaregistrovaný<br /><br /> -1 - odeslání<br /><br /> -2 - publikování<br /><br /> -3 – zamítnuto<br /><br /> -4 - nepublikované|  
|`RegistrationDate`|DateTime|Datum a čas, který byl zaregistrován aplikace.|  
|`CategoryId`|číslo|Kategorie aplikace (Finance, zábava, atd.)|  
|`DeveloperId`|string|Jedinečný identifikátor pro vývojáře, která se odešle aplikace.|  
|`Attachments`|Kolekce [přílohy](#Attachment) entity.|Přílohy pro aplikaci, například snímky obrazovky nebo ikony.|  
|`Icon`|[Přílohy](#Attachment)|Ikona pro aplikaci.|  
  
##  <a name="Attachment"></a> Přílohy  
 `attachment` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`UniqueId`|string|Jedinečný identifikátor pro přílohu.|  
|`Url`|string|Adresa URL prostředku.|  
|`Type`|string|Typ přílohy.|  
|`ContentType`|string|Typ média přílohy.|  
  
##  <a name="Sample"></a> Ukázka kódu  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`title`|string|Název operace.|  
|`snippet`|string|Tato vlastnost je zastaralý a neměl by se používat.|  
|`brush`|string|Které barevné šablony pro použití při zobrazení vzorového kódu syntaxe kódu. Povolené hodnoty jsou `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, a `csharp`.|  
|`template`|string|Název této šablony ukázkový kód.|  
|`body`|string|Zástupný symbol pro část kódu ukázka fragmentu kódu.|  
|`method`|string|Metoda HTTP operace.|  
|`scheme`|string|Protokol, který se má použít pro tento požadavek operation.|  
|`path`|string|Cesta operaci.|  
|`query`|string|Příklad řetězce dotazu s definovanými parametry.|  
|`host`|string|Adresa URL brány služby API Management pro rozhraní API, která obsahuje tuto operaci.|  
|`headers`|Kolekce [záhlaví](#Header) entity.|Hlavičky pro tuto operaci.|  
|`parameters`|Kolekce [parametr](#Parameter) entity.|Parametry, které jsou definovány pro tuto operaci.|  
  
##  <a name="Comment"></a> Komentář  
 `API` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|číslo|Id komentáře.|  
|`CommentText`|string|Text komentáře. Mohou být ve formátu HTML.|  
|`DeveloperCompany`|string|Název společnosti vývojáře.|  
|`PostedOn`|DateTime|Datum a čas publikování komentáře.|  
  
##  <a name="Issue"></a> Problém  
 `issue` Entita má následující vlastnosti.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Jedinečný identifikátor pro tento problém.|  
|`ApiID`|string|Id rozhraní API, pro které tento problém byl nahlášen.|  
|`Title`|string|Název problému.|  
|`Description`|string|Popis problému.|  
|`SubscriptionDeveloperName`|string|Křestní jméno pro vývojáře, které hlásí problém.|  
|`IssueState`|string|Aktuální stav problému. Možné hodnoty jsou navrhované, otevřeno, Uzavřeno.|  
|`ReportedOn`|DateTime|Datum a čas, který tento problém byl nahlášen.|  
|`Comments`|Kolekce [komentář](#Comment) entity.|Komentáře k tomuto problému.|  
|`Attachments`|Kolekce [přílohy](api-management-template-data-model-reference.md#Attachment) entity.|Přílohy k problému.|  
|`Services`|Kolekce [API](#API) entity.|Rozhraní API přihlásit k odběru uživatele, který se nezaznamenaly problém.|  
  
##  <a name="Filtering"></a> Filtrování  
 `filtering` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Pattern`|string|Aktuální hledaný termín; nebo `null` Pokud neexistuje žádný hledaný termín.|  
|`Placeholder`|string|Text k zobrazení na panelu hledání, pokud neexistuje žádný hledaný termín, který je zadán.|  
  
##  <a name="Header"></a> Záhlaví  
 Tato část popisuje `parameter` reprezentace.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|string|Název parametru.|  
|`description`|string|Popis parametru.|  
|`value`|string|Hodnota hlavičky.|  
|`typeName`|string|Datový typ hodnoty hlavičky.|  
|`options`|string|Možnosti.|  
|`required`|Boolean|Určuje, zda záhlaví je povinný.|  
|`readOnly`|Boolean|Určuje, zda je záhlaví jen pro čtení.|  
  
##  <a name="HTTPRequest"></a> Požadavek HTTP  
 Tato část popisuje `request` reprezentace.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`description`|string|Popis žádosti o operaci.|  
|`headers`|pole [záhlaví](#Header) entity.|Hlavičky požadavku.|  
|`parameters`|pole [parametr](#Parameter)|Kolekce parametrů operace žádosti.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce formátovaná žádost o operaci.|  
  
##  <a name="HTTPResponse"></a> Odpověď HTTP  
 Tato část popisuje `response` reprezentace.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`statusCode`|kladné celé číslo|Stavový kód odpovědi pro operaci.|  
|`description`|string|Popis operace odpovědi.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce reprezentace odpověď operace.|  
  
##  <a name="Operation"></a> Operace  
 `operation` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|string|Identifikátor URI. Jednoznačně identifikuje operace v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{aid}/operations/{id}` kde `{aid}` je identifikátor rozhraní API a `{id}` je identifikátor operace. Tato vlastnost je jen pro čtení.|  
|`name`|string|Název operace Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|string|Popis operace. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální počet znaků je 1000 znaků.|  
|`scheme`|string|Popisuje na protokoly, které lze vyvolat operace v tomto rozhraní API. Povolené hodnoty jsou `http`, `https`, nebo obojí `http` a `https`.|  
|`uriTemplate`|string|Určení cílového prostředku pro tuto operaci relativní adresu URL šablony. Může obsahovat parametry. Příklad: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|API Management gateway adresa URL, který je hostitelem rozhraní API.|  
|`httpMethod`|string|Metoda operace HTTP.|  
|`request`|[Požadavek HTTP](#HTTPRequest)|Entita obsahující podrobnosti o žádosti.|  
|`responses`|pole [odpovědi HTTP](#HTTPResponse)|Pole operace [odpověď HTTP](#HTTPResponse) entity.|  
  
##  <a name="Menu"></a> Operace nabídky  
 `operation menu` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`ApiId`|string|Id aktuální rozhraní API.|  
|`CurrentOperationId`|string|Id aktuální operaci.|  
|`Action`|string|Typ nabídky.|  
|`MenuItems`|Kolekce [položky nabídky operace](#MenuItem) entity.|Operace pro aktuální rozhraní API.|  
  
##  <a name="MenuItem"></a> Položka nabídky operace  
 `operation menu item` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Id operace.|  
|`Title`|string|Popis operace.|  
|`HttpMethod`|string|Metoda Http operace.|  
  
##  <a name="Paging"></a> Stránkování  
 `paging` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Page`|číslo|Aktuální číslo stránky.|  
|`PageSize`|číslo|Maximální počet výsledků, který se má zobrazit na jednu stránku.|  
|`TotalItemCount`|číslo|Počet položek k zobrazení.|  
|`ShowAll`|Boolean|Určuje, zda zobrazit všechny výsledky na jednu stránku.|  
|`PageCount`|číslo|Počet stránek výsledků.|  
  
##  <a name="Parameter"></a> Parametr  
 Tato část popisuje `parameter` reprezentace.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|string|Název parametru.|  
|`description`|string|Popis parametru.|  
|`value`|string|Hodnota parametru.|  
|`options`|pole řetězce|Hodnot definovaných pro hodnoty parametru dotazu.|  
|`required`|Boolean|Určuje, zda je parametr povinný či nikoli.|  
|`kind`|číslo|Tento parametr určuje, zda je parametr cesty (1), nebo parametr řetězce dotazu (2).|  
|`typeName`|string|Typ parametru.|  
  
##  <a name="Product"></a> Produkt  
 `product` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Identifikátor URI. Jednoznačně identifikuje produktu v aktuální instanci služby API Management. Hodnota je platná relativní adresa URL ve formátu `products/{pid}` kde `{pid}` je identifikátor produktu. Tato vlastnost je jen pro čtení.|  
|`Title`|string|Název produktu. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`Description`|string|Popis produktu. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální počet znaků je 1000 znaků.|  
|`Terms`|string|Podmínkách použití. Vývojáři k odběru produktu se zobrazí a vyžadovat, abyste přijali tyto podmínky před dokončením procesu předplatného.|  
|`ProductState`|číslo|Určuje, zda je produkt publikovaný, nebo ne. Publikované produkty jsou zjistitelné vývojáři na portálu pro vývojáře. Publikované společností jiných produktů jsou viditelné pouze správcům.<br /><br /> Povolené hodnoty pro stav produktu jsou:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|Boolean|Určuje, zda uživatel může mít několik předplatných k tomuto produktu ve stejnou dobu.|  
|`MultipleSubscriptionsCount`|číslo|Maximální počet předplatných produktu uživatel může mít ve stejnou dobu.|  
  
##  <a name="Provider"></a> Zprostředkovatel  
 `provider` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Properties`|řetězec slovník|Vlastnosti pro tohoto zprostředkovatele ověřování.|  
|`AuthenticationType`|string|Typ zprostředkovatele. (Azure Active Directory, přihlášení k Facebooku, účet Google, Microsoft Account, Twitter).|  
|`Caption`|string|Zobrazovaný název zprostředkovatele.|  
  
##  <a name="Representation"></a> Reprezentace  
 Tato část popisuje `representation`.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`contentType`|string|Určuje typ obsahu registrované nebo vlastní pro reprezentaci, například `application/xml`.|  
|`sample`|string|Příklad vyjádření.|  
  
##  <a name="Subscription"></a> Předplatné  
 `subscription` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Identifikátor URI. Jednoznačně identifikuje předplatného v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}` kde `{sid}` je identifikátor předplatného. Tato vlastnost je jen pro čtení.|  
|`ProductId`|string|Identifikátor prostředku produktu předplacenému produktu. Hodnota je platná relativní adresa URL ve formátu `products/{pid}` kde `{pid}` je identifikátor produktu.|  
|`ProductTitle`|string|Název produktu. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`ProductDescription`|string|Popis produktu. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální počet znaků je 1000 znaků.|  
|`ProductDetailsUrl`|string|Relativní adresa URL pro informace o produktech.|  
|`state`|string|Stav odběru. Možné stavy jsou:<br /><br /> - `0 - suspended` – předplatného je zablokovaná a odběrateli nelze volat jakékoli rozhraní API produktu.<br /><br /> - `1 - active` – je předplatné aktivní.<br /><br /> - `2 - expired` – předplatné dosáhlo datum vypršení platnosti a bylo deaktivováno.<br /><br /> - `3 - submitted` – žádost o odběr provedl pro vývojáře, ale má ještě se schválením nebo zamítnutím.<br /><br /> - `4 - rejected` – předplatné žádost byla zamítnuta správcem.<br /><br /> - `5 - cancelled` – předplatné se zrušil vývojář nebo správce.|  
|`DisplayName`|string|Zobrazovaný název předplatného.|  
|`CreatedDate`|Datum a čas|Datum odběr byl vytvořen, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|Boolean|Určuje, zda lze zrušit předplatné aktuálním uživatelem.|  
|`IsAwaitingApproval`|Boolean|Předplatné Určuje, zda je čeká na schválení.|  
|`StartDate`|Datum a čas|Počáteční datum pro předplatné, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Datum a čas|Datum vypršení platnosti předplatného, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Datum a čas|Datum oznámení pro předplatné, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|Předplatné primární klíč. Maximální délka je 256 znaků.|  
|`secondaryKey`|string|Předplatné sekundární klíč. Maximální délka je 256 znaků.|  
|`CanBeRenewed`|Boolean|Předplatné Určuje, zda jde ji obnovit na aktuálním uživatelem.|  
|`HasExpired`|Boolean|Určuje, zda vypršela platnost předplatného.|  
|`IsRejected`|Boolean|Určuje, zda byla zamítnuta žádost o odběr.|  
|`CancelUrl`|string|Relativní adresa Url pro zrušení odběru.|  
|`RenewUrl`|string|Relativní adresa Url k obnovení předplatného.|  
  
##  <a name="SubscriptionSummary"></a> Souhrn předplatného  
 `subscription summary` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Identifikátor URI. Jednoznačně identifikuje předplatného v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}` kde `{sid}` je identifikátor předplatného. Tato vlastnost je jen pro čtení.|  
|`DisplayName`|string|Zobrazovaný název předplatného|  
  
##  <a name="UserAccountInfo"></a> Informace o uživatelském účtu  
 `user account info` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`FirstName`|string|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`LastName`|string|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`Email`|string|E-mailovou adresu Nesmí být prázdné a musí být jedinečné v rámci instance služby. Maximální délka je 254 znaků.|  
|`Password`|string|Heslo k uživatelskému účtu.|  
|`NameIdentifier`|string|Identifikátor účtu, stejná jako e-mailu uživatele.|  
|`ProviderName`|string|Název zprostředkovatele ověřování.|  
|`IsBasicAccount`|Boolean|Hodnota TRUE, pokud tento účet byl zaregistrován pomocí e-mailu a heslo. false, pokud účet byl zaregistrován pomocí zprostředkovatele.|  
  
##  <a name="UseSignIn"></a> Přihlášení uživatele  
 `user sign in` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Email`|string|E-mailovou adresu Nesmí být prázdné a musí být jedinečné v rámci instance služby. Maximální délka je 254 znaků.|  
|`Password`|string|Heslo k uživatelskému účtu.|  
|`ReturnUrl`|string|Adresa URL stránky, kde uživatel klikl na přihlášení.|  
|`RememberMe`|Boolean|Určuje, zda uložit informace o aktuálním uživateli.|  
|`RegistrationEnabled`|Boolean|Určuje, zda je povolena registrace.|  
|`DelegationEnabled`|Boolean|Určuje, zda je povoleno delegované přihlášení.|  
|`DelegationUrl`|string|Delegované přihlašovací adresa url, pokud je povoleno.|  
|`SsoSignUpUrl`|string|Jednotné přihlašování adresu URL pro uživatele, pokud jsou k dispozici.|  
|`AuxServiceUrl`|string|Pokud je aktuální uživatel správcem, je to odkaz na instanci služby na webu Azure Portal.|  
|`Providers`|Kolekce [poskytovatele](#Provider) entity|Zprostředkovatelé ověřování pro tohoto uživatele.|  
|`UserRegistrationTerms`|string|Podmínky, které uživatel musí vyjádřit souhlas s před přihlášením.|  
|`UserRegistrationTermsEnabled`|Boolean|Určuje, zda jsou povoleny podmínky.|  
  
##  <a name="UserSignUp"></a> Registrace uživatele  
 `user sign up` Entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|Boolean|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)Zaregistrujte ovládací prvek.|  
|`Password`|string|Heslo k uživatelskému účtu.|  
|`PasswordVerdictLevel`|číslo|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)Zaregistrujte ovládací prvek.|  
|`UserRegistrationTerms`|string|Podmínky, které uživatel musí vyjádřit souhlas s před přihlášením.|  
|`UserRegistrationTermsOptions`|číslo|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)Zaregistrujte ovládací prvek.|  
|`ConsentAccepted`|Boolean|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)Zaregistrujte ovládací prvek.|  
|`Email`|string|E-mailovou adresu Nesmí být prázdné a musí být jedinečné v rámci instance služby. Maximální délka je 254 znaků.|  
|`FirstName`|string|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`LastName`|string|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`UserData`|string|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up) ovládacího prvku.|  
|`NameIdentifier`|string|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)Zaregistrujte ovládací prvek.|  
|`ProviderName`|string|Název zprostředkovatele ověřování.|

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
