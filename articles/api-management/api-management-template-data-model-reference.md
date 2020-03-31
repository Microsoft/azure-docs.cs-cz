---
title: Odkaz na datový model šablony azure api managementu | Dokumenty společnosti Microsoft
description: Přečtěte si o reprezentacích entit a typů pro běžné položky používané v datových modelech pro šablony portálu pro vývojáře ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 5625ff7e4fc51b9b6b894698719247902a480f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243936"
---
# <a name="azure-api-management-template-data-model-reference"></a>Odkaz na datový model šablony azure api managementu
Toto téma popisuje reprezentace entit a typů pro běžné položky používané v datových modelech pro šablony portálu pro vývojáře ve správě rozhraní Azure API.  
  
 Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Odkaz

-   [rozhraní API](#API)  
-   [Souhrn rozhraní API](#APISummary)  
-   [Aplikace](#Application)  
-   [Příloha](#Attachment)  
-   [Ukázka kódu](#Sample)  
-   [Komentář](#Comment)  
-   [Filtrování](#Filtering)  
-   [Záhlaví](#Header)  
-   [Požadavek HTTP](#HTTPRequest)  
-   [Odpověď HTTP](#HTTPResponse)  
-   [Problém](#Issue)  
-   [Operace](#Operation)  
-   [Operační menu](#Menu)  
-   [Položka nabídky Operace](#MenuItem)  
-   [Stránkování](#Paging)  
-   [Parametr](#Parameter)  
-   [Produktu](#Product)  
-   [Poskytovatel](#Provider)  
-   [Reprezentace](#Representation)  
-   [Předplatné](#Subscription)  
-   [Souhrn předplatného](#SubscriptionSummary)  
-   [Informace o uživatelském účtu](#UserAccountInfo)  
-   [Přihlášení uživatele](#UseSignIn)  
-   [Registrace uživatele](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a>Rozhraní api  
 Entita `API` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|řetězec|Identifikátor prostředku. Jednoznačně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL `apis/{id}` `{id}` ve formátu kde je identifikátor rozhraní API. Tato vlastnost je jen ke čtení.|  
|`name`|řetězec|Název rozhraní API. Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`description`|řetězec|Popis rozhraní API. Nesmí být prázdná. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`serviceUrl`|řetězec|Absolutní adresa URL back-endové služby implementující toto rozhraní API.|  
|`path`|řetězec|Relativní adresa URL jednoznačně identifikuje toto rozhraní API a všechny jeho cesty prostředků v rámci instance služby Správa rozhraní API. Je připojen k základní adrese URL koncového bodu rozhraní API určené během vytváření instance služby, aby vytvořil veřejnou adresu URL pro toto rozhraní API.|  
|`protocols`|pole čísla|Popisuje, na kterých protokolech lze vyvolat operace v tomto rozhraní API. Povolené hodnoty `1 - http` `2 - https`jsou a , nebo obojí.|  
|`authenticationSettings`|[Nastavení ověřování autorizačního serveru](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekce nastavení ověřování zahrnutých v tomto rozhraní API.|  
|`subscriptionKeyParameterNames`|objekt|Volitelná vlastnost, kterou lze použít k určení vlastních názvů parametrů dotazu nebo záhlaví obsahující klíč odběru. Pokud je tato vlastnost k dispozici, musí obsahovat alespoň jednu ze dvou následujících vlastností.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a>Souhrn rozhraní API  
 Entita `API summary` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|řetězec|Identifikátor prostředku. Jednoznačně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL `apis/{id}` `{id}` ve formátu kde je identifikátor rozhraní API. Tato vlastnost je jen ke čtení.|  
|`name`|řetězec|Název rozhraní API. Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`description`|řetězec|Popis rozhraní API. Nesmí být prázdná. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
  
##  <a name="application"></a><a name="Application"></a>Aplikace  
 Entita `application` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Jedinečný identifikátor aplikace.|  
|`Title`|řetězec|Název aplikace.|  
|`Description`|řetězec|Popis aplikace.|  
|`Url`|Identifikátor URI|Identifikátor URI pro aplikaci.|  
|`Version`|řetězec|Informace o verzi aplikace.|  
|`Requirements`|řetězec|Popis požadavků pro aplikaci.|  
|`State`|číslo|Aktuální stav aplikace.<br /><br /> - 0 - Registrováno<br /><br /> - 1 - Odesláno<br /><br /> - 2 - Zveřejněno<br /><br /> - 3 - Odmítnuto<br /><br /> - 4 - Nezveřejněno|  
|`RegistrationDate`|DateTime|Datum a čas registrace aplikace.|  
|`CategoryId`|číslo|Kategorie aplikace (Finance, zábava, atd.)|  
|`DeveloperId`|řetězec|Jedinečný identifikátor vývojáře, který odeslal aplikaci.|  
|`Attachments`|Kolekce entit [Příloha.](#Attachment)|Všechny přílohy pro aplikaci, jako jsou snímky obrazovky nebo ikony.|  
|`Icon`|[Příloha](#Attachment)|Ikona pro aplikaci.|  
  
##  <a name="attachment"></a><a name="Attachment"></a>Přílohu  
 Entita `attachment` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`UniqueId`|řetězec|Jedinečný identifikátor přílohy.|  
|`Url`|řetězec|Adresa URL prostředku.|  
|`Type`|řetězec|Typ přílohy.|  
|`ContentType`|řetězec|Typ média přílohy.|  
  
##  <a name="code-sample"></a><a name="Sample"></a>Ukázka kódu  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`title`|řetězec|Název operace.|  
|`snippet`|řetězec|Tato vlastnost je zastaralé a by neměly být používány.|  
|`brush`|řetězec|Která šablona vybarvení syntaxe kódu, která má být použita při zobrazení ukázky kódu. Povolené hodnoty `plain` `php`jsou `java` `xml`, `objc` `python`, `ruby`, `csharp`, , , a .|  
|`template`|řetězec|Název této ukázkové šablony kódu.|  
|`body`|řetězec|Zástupný symbol pro ukázkovou část fragmentu kódu.|  
|`method`|řetězec|Metoda HTTP operace.|  
|`scheme`|řetězec|Protokol, který má být určen pro požadavek na operaci.|  
|`path`|řetězec|Cesta operace.|  
|`query`|řetězec|Příklad řetězce dotazu s definovanými parametry.|  
|`host`|řetězec|Adresa URL brány služby Api management pro rozhraní API, které obsahuje tuto operaci.|  
|`headers`|Kolekce entit [Záhlaví.](#Header)|Záhlaví pro tuto operaci.|  
|`parameters`|Kolekce entit [Parametr.](#Parameter)|Parametry, které jsou definovány pro tuto operaci.|  
  
##  <a name="comment"></a><a name="Comment"></a>Komentář  
 Entita `API` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|číslo|ID komentáře.|  
|`CommentText`|řetězec|Tělo komentáře. Může obsahovat HTML.|  
|`DeveloperCompany`|řetězec|Název společnosti vývojáře.|  
|`PostedOn`|DateTime|Datum a čas, kdy byl komentář zaúčtován.|  
  
##  <a name="issue"></a><a name="Issue"></a>Problém  
 Entita `issue` má následující vlastnosti.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Jedinečný identifikátor problému.|  
|`ApiID`|řetězec|ID rozhraní API, pro které byl tento problém hlášen.|  
|`Title`|řetězec|Název vydání.|  
|`Description`|řetězec|Popis problému.|  
|`SubscriptionDeveloperName`|řetězec|Křestní jméno vývojáře, který ohlásil problém.|  
|`IssueState`|řetězec|Aktuální stav problému. Možné hodnoty jsou Navrženo, Otevřeno, Uzavřeno.|  
|`ReportedOn`|DateTime|Datum a čas, kdy byl problém nahlášen.|  
|`Comments`|Kolekce entity [komentář.](#Comment)|Připomínky k této problematice.|  
|`Attachments`|Kolekce entit [Příloha.](api-management-template-data-model-reference.md#Attachment)|Jakékoli přílohy k problému.|  
|`Services`|Kolekce entit [rozhraní API.](#API)|Rozhraní API, k jejichž odběru se přihlásil uživatel, který problém podal.|  
  
##  <a name="filtering"></a><a name="Filtering"></a>Filtrování  
 Entita `filtering` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Pattern`|řetězec|Aktuální hledaný výraz; nebo `null` pokud neexistuje žádný vyhledávací dotaz.|  
|`Placeholder`|řetězec|Text, který se má zobrazit ve vyhledávacím poli, pokud není zadán žádný hledaný termín.|  
  
##  <a name="header"></a><a name="Header"></a>Záhlaví  
 Tato část popisuje `parameter` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|řetězec|Názvy parametrů.|  
|`description`|řetězec|Popis parametru.|  
|`value`|řetězec|Hodnota záhlaví.|  
|`typeName`|řetězec|Datový typ hodnoty záhlaví.|  
|`options`|řetězec|Možnosti.|  
|`required`|Boolean|Určuje, zda je záhlaví povinné.|  
|`readOnly`|Boolean|Určuje, zda je záhlaví jen pro čtení.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a>Požadavek HTTP  
 Tato část popisuje `request` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`description`|řetězec|Popis požadavku operace.|  
|`headers`|pole entit [Záhlaví.](#Header)|Vyžádejte si záhlaví.|  
|`parameters`|pole [parametru](#Parameter)|Kolekce parametrů požadavku operace.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce reprezentací požadavků na operaci.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a>Odpověď HTTP  
 Tato část popisuje `response` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`statusCode`|kladné celé číslo|Stavový kód odezvy operace.|  
|`description`|řetězec|Popis odezvy operace.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce reprezentací odezvy operace.|  
  
##  <a name="operation"></a><a name="Operation"></a>Operace  
 Entita `operation` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|řetězec|Identifikátor prostředku. Jednoznačně identifikuje operaci v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL `apis/{aid}/operations/{id}` `{aid}` ve formátu kde `{id}` je identifikátor rozhraní API a je identifikátor operace. Tato vlastnost je jen ke čtení.|  
|`name`|řetězec|Název operace. Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`description`|řetězec|Popis operace. Nesmí být prázdná. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`scheme`|řetězec|Popisuje, na kterých protokolech lze vyvolat operace v tomto rozhraní API. Povolené hodnoty `http` `https`jsou , `http` `https`nebo obojí a .|  
|`uriTemplate`|řetězec|Relativní šablona adresy URL identifikující cílový prostředek pro tuto operaci. Může obsahovat parametry. Příklad: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|řetězec|Adresa URL brány pro správu rozhraní API, která je hostitelem rozhraní API.|  
|`httpMethod`|řetězec|Operace HTTP metoda.|  
|`request`|[Požadavek HTTP](#HTTPRequest)|Entita obsahující podrobnosti požadavku.|  
|`responses`|pole [odpovědi HTTP](#HTTPResponse)|Pole operací [entit odpovědi HTTP.](#HTTPResponse)|  
  
##  <a name="operation-menu"></a><a name="Menu"></a>Operační menu  
 Entita `operation menu` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`ApiId`|řetězec|ID aktuální rozhraní API.|  
|`CurrentOperationId`|řetězec|ID aktuální operace.|  
|`Action`|řetězec|Typ nabídky.|  
|`MenuItems`|Kolekce entit [položky nabídky Operace.](#MenuItem)|Operace pro aktuální rozhraní API.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a>Položka nabídky Operace  
 Entita `operation menu item` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|ID operace.|  
|`Title`|řetězec|Popis operace.|  
|`HttpMethod`|řetězec|Metoda http operace.|  
  
##  <a name="paging"></a><a name="Paging"></a>Stránkování  
 Entita `paging` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Page`|číslo|Aktuální číslo stránky.|  
|`PageSize`|číslo|Maximální výsledky, které mají být zobrazeny na jedné stránce.|  
|`TotalItemCount`|číslo|Počet položek pro zobrazení.|  
|`ShowAll`|Boolean|Zda sho všechny výsledky na jedné stránce.|  
|`PageCount`|číslo|Počet stránek výsledků.|  
  
##  <a name="parameter"></a><a name="Parameter"></a>Parametr  
 Tato část popisuje `parameter` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|řetězec|Názvy parametrů.|  
|`description`|řetězec|Popis parametru.|  
|`value`|řetězec|Hodnota parametru.|  
|`options`|pole řetězce|Hodnoty definované pro hodnoty parametrů dotazu.|  
|`required`|Boolean|Určuje, zda je parametr povinný nebo ne.|  
|`kind`|číslo|Určuje, zda je tento parametr parametrem cesty (1) nebo parametrem řetězce dotazu (2).|  
|`typeName`|řetězec|Typ parametru.|  
  
##  <a name="product"></a><a name="Product"></a>Produktu  
 Entita `product` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Identifikátor prostředku. Jednoznačně identifikuje produkt v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL `products/{pid}` `{pid}` ve formátu, kde je identifikátor produktu. Tato vlastnost je jen ke čtení.|  
|`Title`|řetězec|Název produktu Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`Description`|řetězec|Popis produktu. Nesmí být prázdná. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`Terms`|řetězec|Podmínky použití produktu. Vývojáři, kteří se pokusí přihlásit k odběru produktu, budou před dokončením procesu předplatného předloženi a budou muset tyto podmínky přijmout.|  
|`ProductState`|číslo|Určuje, zda je produkt publikován či nikoli. Publikované produkty jsou zjistitelné vývojáři na portálu pro vývojáře. Nepublikované produkty jsou viditelné pouze správcům.<br /><br /> Přípustné hodnoty pro stav produktu jsou:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|Boolean|Určuje, zda může mít uživatel více předplatných tohoto produktu současně.|  
|`MultipleSubscriptionsCount`|číslo|Maximální počet předplatných tohoto produktu, které uživatel může mít současně.|  
  
##  <a name="provider"></a><a name="Provider"></a>Zprostředkovatel  
 Entita `provider` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Properties`|slovník řetězců|Vlastnosti tohoto zprostředkovatele ověřování.|  
|`AuthenticationType`|řetězec|Typ zprostředkovatele. (Azure Active Directory, Přihlášení na Facebooku, Účet Google, Účet Microsoft, Twitter).|  
|`Caption`|řetězec|Zobrazovaný název zprostředkovatele.|  
  
##  <a name="representation"></a><a name="Representation"></a>Reprezentace  
 Tato část popisuje `representation`.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`contentType`|řetězec|Určuje registrovaný nebo vlastní typ obsahu pro tuto `application/xml`reprezentaci, například .|  
|`sample`|řetězec|Příklad reprezentace.|  
  
##  <a name="subscription"></a><a name="Subscription"></a>Předplatné  
 Entita `subscription` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Identifikátor prostředku. Jednoznačně identifikuje předplatné v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL `subscriptions/{sid}` `{sid}` ve formátu kde je identifikátor předplatného. Tato vlastnost je jen ke čtení.|  
|`ProductId`|řetězec|Identifikátor prostředku produktu upsaného produktu. Hodnota je platná relativní adresa URL `products/{pid}` `{pid}` ve formátu, kde je identifikátor produktu.|  
|`ProductTitle`|řetězec|Název produktu Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`ProductDescription`|řetězec|Popis produktu. Nesmí být prázdná. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`ProductDetailsUrl`|řetězec|Relativní adresa URL k podrobnostem produktu.|  
|`state`|řetězec|Stav předplatného. Možné stavy jsou:<br /><br /> - `0 - suspended`– předplatné je blokováno a odběratel nemůže volat žádná api produktu.<br /><br /> - `1 - active`– předplatné je aktivní.<br /><br /> - `2 - expired`– předplatné dosáhlo data vypršení platnosti a bylo deaktivováno.<br /><br /> - `3 - submitted`– žádost o předplatné byla podána vývojářem, ale dosud nebyla schválena nebo zamítnuta.<br /><br /> - `4 - rejected`– požadavek na předplatné byl zamítnut správcem.<br /><br /> - `5 - cancelled`– vývojář nebo správce zrušil předplatné.|  
|`DisplayName`|řetězec|Zobrazovaný název předplatného.|  
|`CreatedDate`|data a času.|Datum vytvoření předplatného ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|Boolean|Určuje, zda může být předplatné aktuálním uživatelem zrušeno.|  
|`IsAwaitingApproval`|Boolean|Zda předplatné čeká na schválení.|  
|`StartDate`|data a času.|Počáteční datum předplatného ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|data a času.|Datum vypršení platnosti předplatného ve formátu ISO `2014-06-24T16:25:00Z`8601: .|  
|`NotificationDate`|data a času.|Datum oznámení předplatného ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|řetězec|Primární klíč předplatného. Maximální délka je 256 znaků.|  
|`secondaryKey`|řetězec|Sekundární klíč předplatného. Maximální délka je 256 znaků.|  
|`CanBeRenewed`|Boolean|Určuje, zda může být předplatné obnoveno aktuálním uživatelem.|  
|`HasExpired`|Boolean|Zda vypršela platnost předplatného.|  
|`IsRejected`|Boolean|Zda byl požadavek na odběr zamítnut.|  
|`CancelUrl`|řetězec|Relativní url zrušit předplatné.|  
|`RenewUrl`|řetězec|Relativní url obnovit předplatné.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a>Souhrn předplatného  
 Entita `subscription summary` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Identifikátor prostředku. Jednoznačně identifikuje předplatné v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL `subscriptions/{sid}` `{sid}` ve formátu kde je identifikátor předplatného. Tato vlastnost je jen ke čtení.|  
|`DisplayName`|řetězec|Zobrazovaný název předplatného|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a>Informace o uživatelském účtu  
 Entita `user account info` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`FirstName`|řetězec|Křestní jméno. Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`LastName`|řetězec|Příjmení. Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`Email`|řetězec|E-mailovou adresu Nesmí být prázdný a musí být jedinečný v rámci instance služby. Maximální délka je 254 znaků.|  
|`Password`|řetězec|Heslo uživatelského účtu.|  
|`NameIdentifier`|řetězec|Identifikátor účtu, stejný jako e-mail uživatele.|  
|`ProviderName`|řetězec|Název zprostředkovatele ověřování.|  
|`IsBasicAccount`|Boolean|True, pokud byl tento účet registrován pomocí e-mailu a hesla; false, pokud byl účet registrován pomocí zprostředkovatele.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a>Přihlášení uživatelem  
 Entita `user sign in` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Email`|řetězec|E-mailovou adresu Nesmí být prázdný a musí být jedinečný v rámci instance služby. Maximální délka je 254 znaků.|  
|`Password`|řetězec|Heslo uživatelského účtu.|  
|`ReturnUrl`|řetězec|Adresa URL stránky, na kterou uživatel kliknul, aby se přihlásil.|  
|`RememberMe`|Boolean|Určuje, zda se mají uložit informace o aktuálním uživateli.|  
|`RegistrationEnabled`|Boolean|Zda je povolena registrace.|  
|`DelegationEnabled`|Boolean|Zda je povoleno delegované přihlášení.|  
|`DelegationUrl`|řetězec|Delegovaná adresa URL přihlášení, pokud je povolena.|  
|`SsoSignUpUrl`|řetězec|Adresa URL jednotného přihlášení pro uživatele, pokud je k dispozici.|  
|`AuxServiceUrl`|řetězec|Pokud je aktuální uživatel správce, jedná se o odkaz na instanci služby na webu Azure Portal.|  
|`Providers`|Kolekce entit [poskytovatele](#Provider)|Zprostředkovatelé ověřování pro tohoto uživatele.|  
|`UserRegistrationTerms`|řetězec|Podmínky, se kterými musí uživatel souhlasit před přihlášením.|  
|`UserRegistrationTermsEnabled`|Boolean|Určuje, zda jsou povoleny podmínky.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a>Registrace uživatele  
 Entita `user sign up` má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|Boolean|Hodnota [používaná](api-management-page-controls.md#sign-up)ovládacím prvkem registrace.|  
|`Password`|řetězec|Heslo uživatelského účtu.|  
|`PasswordVerdictLevel`|číslo|Hodnota [používaná](api-management-page-controls.md#sign-up)ovládacím prvkem registrace.|  
|`UserRegistrationTerms`|řetězec|Podmínky, se kterými musí uživatel souhlasit před přihlášením.|  
|`UserRegistrationTermsOptions`|číslo|Hodnota [používaná](api-management-page-controls.md#sign-up)ovládacím prvkem registrace.|  
|`ConsentAccepted`|Boolean|Hodnota [používaná](api-management-page-controls.md#sign-up)ovládacím prvkem registrace.|  
|`Email`|řetězec|E-mailovou adresu Nesmí být prázdný a musí být jedinečný v rámci instance služby. Maximální délka je 254 znaků.|  
|`FirstName`|řetězec|Křestní jméno. Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`LastName`|řetězec|Příjmení. Nesmí být prázdná. Maximální délka je 100 znaků.|  
|`UserData`|řetězec|Hodnota používaná ovládacím prvkem [registrace.](api-management-page-controls.md#sign-up)|  
|`NameIdentifier`|řetězec|Hodnota [používaná](api-management-page-controls.md#sign-up)ovládacím prvkem registrace.|  
|`ProviderName`|řetězec|Název zprostředkovatele ověřování.|

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](api-management-developer-portal-templates.md).
