---
title: Referenční informace k datovému modelu šablony Azure API Management | Microsoft Docs
description: Přečtěte si informace o entitách a typech reprezentace pro běžné položky používané v datových modelech pro šablony portálu pro vývojáře v Azure API Management.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243936"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referenční informace k datovému modelu šablony Azure API Management
Toto téma popisuje entity a typy reprezentace pro běžné položky používané v datových modelech pro šablony portálu pro vývojáře v Azure API Management.  
  
 Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Odkaz

-   [Rozhraní API](#API)  
-   [Přehled rozhraní API](#APISummary)  
-   [Použití](#Application)  
-   [Upevněn](#Attachment)  
-   [Ukázka kódu](#Sample)  
-   [Vytvořena](#Comment)  
-   [Jakou](#Filtering)  
-   [Hlavička](#Header)  
-   [Požadavek HTTP](#HTTPRequest)  
-   [Odpověď HTTP](#HTTPResponse)  
-   [Chybu](#Issue)  
-   [Operace](#Operation)  
-   [Nabídka operace](#Menu)  
-   [Položka nabídky operace](#MenuItem)  
-   [Přenosu](#Paging)  
-   [Ukazatele](#Parameter)  
-   [Produktu](#Product)  
-   [Poskytovatel](#Provider)  
-   [Obrázek](#Representation)  
-   [Předplatné](#Subscription)  
-   [Souhrn předplatných](#SubscriptionSummary)  
-   [Informace o uživatelském účtu](#UserAccountInfo)  
-   [Přihlášení uživatele](#UseSignIn)  
-   [Registrace uživatele](#UserSignUp)  
  
##  <a name="API"></a>API  
 `API` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|řetězec|Identifikátor prostředku Jedinečně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}`, kde `{id}` je identifikátor rozhraní API. Tato vlastnost je jen ke čtení.|  
|`name`|řetězec|Název rozhraní API Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|řetězec|Popis rozhraní API Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`serviceUrl`|řetězec|Absolutní adresa URL back-end služby implementující toto rozhraní API.|  
|`path`|řetězec|Relativní adresa URL jednoznačně identifikuje toto rozhraní API a všechny jeho cesty k prostředkům v rámci instance služby API Management. Připojí se k základní adrese URL koncového bodu rozhraní API zadané během vytváření instance služby za účelem vytvoření veřejné adresy URL pro toto rozhraní API.|  
|`protocols`|pole čísla|V této části najdete popis protokolů, které mohou být operace v tomto rozhraní API vyvolány. Povolené hodnoty jsou `1 - http` a `2 - https`, nebo obojí.|  
|`authenticationSettings`|[Nastavení ověřování autorizačního serveru](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekce nastavení ověřování zahrnutá v tomto rozhraní API|  
|`subscriptionKeyParameterNames`|object|Volitelná vlastnost, která se dá použít k zadání vlastních názvů pro parametry dotazu nebo hlavičky obsahující klíč předplatného Pokud je tato vlastnost přítomna, musí obsahovat alespoň jednu ze dvou následujících vlastností.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Přehled rozhraní API  
 `API summary` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|řetězec|Identifikátor prostředku Jedinečně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}`, kde `{id}` je identifikátor rozhraní API. Tato vlastnost je jen ke čtení.|  
|`name`|řetězec|Název rozhraní API Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|řetězec|Popis rozhraní API Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
  
##  <a name="Application"></a>Použití  
 `application` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Jedinečný identifikátor aplikace|  
|`Title`|řetězec|Název aplikace|  
|`Description`|řetězec|Popis aplikace|  
|`Url`|Identifikátor URI|Identifikátor URI pro aplikaci|  
|`Version`|řetězec|Informace o verzi aplikace|  
|`Requirements`|řetězec|Popis požadavků pro aplikaci.|  
|`State`|number|Aktuální stav aplikace<br /><br /> -0 – registrováno<br /><br /> -1-odesláno<br /><br /> -2 – Publikováno<br /><br /> -3 – zamítnuto<br /><br /> -4 – nepublikováno|  
|`RegistrationDate`|DateTime|Datum a čas, kdy byla aplikace zaregistrována.|  
|`CategoryId`|number|Kategorie aplikace (finance, zábava atd.)|  
|`DeveloperId`|řetězec|Jedinečný identifikátor vývojáře, který aplikaci odeslal.|  
|`Attachments`|Kolekce entit [příloh](#Attachment)|Jakékoli přílohy pro aplikaci, jako jsou snímky obrazovky nebo ikony.|  
|`Icon`|[Upevněn](#Attachment)|Ikona pro aplikaci|  
  
##  <a name="Attachment"></a>Upevněn  
 `attachment` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`UniqueId`|řetězec|Jedinečný identifikátor pro přílohu|  
|`Url`|řetězec|Adresa URL prostředku|  
|`Type`|řetězec|Typ přílohy|  
|`ContentType`|řetězec|Typ média přílohy|  
  
##  <a name="Sample"></a>Ukázka kódu  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`title`|řetězec|Název operace.|  
|`snippet`|řetězec|Tato vlastnost je zastaralá a neměla by se používat.|  
|`brush`|řetězec|Která vybarvení syntaxe kódu má být použita při zobrazení ukázky kódu. Povolené hodnoty jsou `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`a `csharp`.|  
|`template`|řetězec|Název této šablony vzorového kódu.|  
|`body`|řetězec|Zástupný symbol pro ukázkovou část kódu fragmentu.|  
|`method`|řetězec|Metoda HTTP operace.|  
|`scheme`|řetězec|Protokol, který se má použít pro požadavek operace.|  
|`path`|řetězec|Cesta k operaci.|  
|`query`|řetězec|Příklad řetězce dotazu s definovanými parametry|  
|`host`|řetězec|Adresa URL brány API Management služby pro rozhraní API, které obsahuje tuto operaci.|  
|`headers`|Kolekce entit [hlaviček](#Header)|Hlavičky pro tuto operaci|  
|`parameters`|Kolekce entit [parametrů](#Parameter)|Parametry, které jsou definovány pro tuto operaci.|  
  
##  <a name="Comment"></a>Vytvořena  
 `API` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|number|ID komentáře|  
|`CommentText`|řetězec|Tělo komentáře Může zahrnovat HTML.|  
|`DeveloperCompany`|řetězec|Název společnosti pro vývojáře|  
|`PostedOn`|DateTime|Datum a čas, kdy byla Poznámka publikována|  
  
##  <a name="Issue"></a>Chybu  
 `issue` entita má následující vlastnosti.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Jedinečný identifikátor problému.|  
|`ApiID`|řetězec|ID pro rozhraní API, pro které se tento problém nahlásil.|  
|`Title`|řetězec|Název problému|  
|`Description`|řetězec|Popis problému|  
|`SubscriptionDeveloperName`|řetězec|Křestní jméno vývojáře, který nahlásil problém.|  
|`IssueState`|řetězec|Aktuální stav problému. Možné hodnoty jsou navrhovány, otevřeny, uzavřeny.|  
|`ReportedOn`|DateTime|Datum a čas hlášení problému.|  
|`Comments`|Kolekce entit [komentářů](#Comment)|Komentáře k tomuto problému.|  
|`Attachments`|Kolekce entit [příloh](api-management-template-data-model-reference.md#Attachment)|Jakékoli přílohy k problému.|  
|`Services`|Kolekce entit [rozhraní API](#API) .|Rozhraní API, která se přihlásila k odběru uživatelem, který problém vyřešil.|  
  
##  <a name="Filtering"></a>Jakou  
 `filtering` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Pattern`|řetězec|Aktuální hledaný výraz; nebo `null`, pokud neexistuje hledaný termín.|  
|`Placeholder`|řetězec|Text, který se má zobrazit v poli hledání, pokud není zadán žádný hledaný termín.|  
  
##  <a name="Header"></a>Hlaviček  
 Tato část popisuje `parameter` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|řetězec|Název parametru.|  
|`description`|řetězec|Popis parametru|  
|`value`|řetězec|Hodnota hlavičky|  
|`typeName`|řetězec|Datový typ hodnoty hlavičky|  
|`options`|řetězec|Nastavení.|  
|`required`|Datový typ Boolean|Určuje, zda je záhlaví vyžadováno.|  
|`readOnly`|Datový typ Boolean|Určuje, zda je hlavička určena pouze pro čtení.|  
  
##  <a name="HTTPRequest"></a>Požadavek HTTP  
 Tato část popisuje `request` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`description`|řetězec|Popis požadavku na operaci|  
|`headers`|pole entit [záhlaví](#Header)|Hlavičky požadavku.|  
|`parameters`|pole [parametru](#Parameter)|Kolekce parametrů požadavku operace.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce reprezentace požadavků operace|  
  
##  <a name="HTTPResponse"></a>Odpověď HTTP  
 Tato část popisuje `response` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`statusCode`|Kladné celé číslo|Stavový kód odezvy operace.|  
|`description`|řetězec|Popis odezvy operace.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce reprezentace odezvy operací|  
  
##  <a name="Operation"></a>NázevOperace  
 `operation` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|řetězec|Identifikátor prostředku Jednoznačně identifikuje operaci v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{aid}/operations/{id}`, kde `{aid}` je identifikátor rozhraní API a `{id}` je identifikátor operace. Tato vlastnost je jen ke čtení.|  
|`name`|řetězec|Název operace Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|řetězec|Popis operace Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`scheme`|řetězec|V této části najdete popis protokolů, které mohou být operace v tomto rozhraní API vyvolány. Povolené hodnoty jsou `http`, `https`nebo `http` i `https`.|  
|`uriTemplate`|řetězec|Šablona relativní adresy URL identifikující cílový prostředek pro tuto operaci. Může obsahovat parametry. Příklad: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|řetězec|Adresa URL API Management brány, která hostuje rozhraní API.|  
|`httpMethod`|řetězec|Metoda HTTP operace.|  
|`request`|[Požadavek HTTP](#HTTPRequest)|Entita obsahující podrobnosti žádosti|  
|`responses`|pole [odpovědi HTTP](#HTTPResponse)|Pole entit s [odpovědí HTTP](#HTTPResponse) operace.|  
  
##  <a name="Menu"></a>Nabídka operace  
 `operation menu` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`ApiId`|řetězec|ID aktuálního rozhraní API|  
|`CurrentOperationId`|řetězec|ID aktuální operace|  
|`Action`|řetězec|Typ nabídky|  
|`MenuItems`|Kolekce entit [položek nabídky operace](#MenuItem)|Operace pro aktuální rozhraní API.|  
  
##  <a name="MenuItem"></a>Položka nabídky operace  
 `operation menu item` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|ID operace.|  
|`Title`|řetězec|Popis operace.|  
|`HttpMethod`|řetězec|Metoda HTTP operace.|  
  
##  <a name="Paging"></a>Přenosu  
 `paging` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Page`|number|Číslo aktuální stránky|  
|`PageSize`|number|Maximální výsledky, které mají být zobrazeny na jedné stránce.|  
|`TotalItemCount`|number|Počet položek, které se mají zobrazit|  
|`ShowAll`|Datový typ Boolean|Určuje, zda se mají zobrazit všechny výsledky na jedné stránce.|  
|`PageCount`|number|Počet stránek výsledků.|  
  
##  <a name="Parameter"></a>Ukazatele  
 Tato část popisuje `parameter` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|řetězec|Název parametru.|  
|`description`|řetězec|Popis parametru|  
|`value`|řetězec|Hodnota parametru.|  
|`options`|pole řetězce|Hodnoty definované pro hodnoty parametrů dotazu.|  
|`required`|Datový typ Boolean|Určuje, jestli je parametr povinný, nebo ne.|  
|`kind`|number|Určuje, zda je tento parametr parametr cesty (1) nebo parametr QueryString (2).|  
|`typeName`|řetězec|Typ parametru.|  
  
##  <a name="Product"></a>Produktu  
 `product` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Identifikátor prostředku Jednoznačně identifikuje produkt v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `products/{pid}`, kde `{pid}` je identifikátor produktu. Tato vlastnost je jen ke čtení.|  
|`Title`|řetězec|Název produktu Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`Description`|řetězec|Popis produktu Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`Terms`|řetězec|Produktové podmínkami použití. Vývojářům, kteří se pokoušejí přihlásit k odběru produktu, se zobrazí a bude muset tyto podmínky přijmout, aby mohli dokončit proces předplatného.|  
|`ProductState`|number|Určuje, jestli je produkt publikovaný, nebo ne. Publikované produkty můžou využít vývojáři na portálu pro vývojáře. Nepublikované produkty jsou viditelné pouze správcům.<br /><br /> Povolené hodnoty pro stav produktu jsou:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|Datový typ Boolean|Určuje, jestli uživatel může mít k tomuto produktu víc předplatných.|  
|`MultipleSubscriptionsCount`|number|Maximální počet předplatných pro tento produkt může mít uživatel ve stejnou dobu.|  
  
##  <a name="Provider"></a>Zprostředkovatele  
 `provider` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Properties`|slovník řetězců|Vlastnosti tohoto zprostředkovatele ověřování.|  
|`AuthenticationType`|řetězec|Typ poskytovatele. (Azure Active Directory, přihlášení na Facebooku, účet Google, účet Microsoft, Twitter).|  
|`Caption`|řetězec|Zobrazovaný název zprostředkovatele|  
  
##  <a name="Representation"></a>Obrázek  
 Tato část popisuje `representation`.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`contentType`|řetězec|Určuje registrovaný nebo vlastní typ obsahu pro toto znázornění, například `application/xml`.|  
|`sample`|řetězec|Příklad reprezentace.|  
  
##  <a name="Subscription"></a>Formě  
 `subscription` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Identifikátor prostředku Jednoznačně identifikuje odběr v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}`, kde `{sid}` je identifikátor předplatného. Tato vlastnost je jen ke čtení.|  
|`ProductId`|řetězec|Identifikátor prostředku produktu odebíraného produktu. Hodnota je platná relativní adresa URL ve formátu `products/{pid}`, kde `{pid}` je identifikátor produktu.|  
|`ProductTitle`|řetězec|Název produktu Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`ProductDescription`|řetězec|Popis produktu Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`ProductDetailsUrl`|řetězec|Relativní adresa URL s podrobnostmi o produktu|  
|`state`|řetězec|Stav odběru Možné stavy:<br /><br /> - `0 - suspended` – předplatné je blokované a předplatitel nemůže volat žádná rozhraní API produktu.<br /><br /> - `1 - active` – předplatné je aktivní.<br /><br /> - `2 - expired` – předplatné dosáhlo data vypršení platnosti a bylo deaktivováno.<br /><br /> - `3 - submitted` – žádost o předplatné udělal vývojář, ale ještě nebyla schválená ani zamítnutá.<br /><br /> - `4 - rejected` – žádost o předplatné byla zamítnuta správcem.<br /><br /> - `5 - cancelled` – předplatné zrušila vývojář nebo správce.|  
|`DisplayName`|řetězec|Zobrazovaný název předplatného|  
|`CreatedDate`|Datum a čas|Datum vytvoření odběru ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|Datový typ Boolean|Zda může být předplatné zrušeno aktuálním uživatelem.|  
|`IsAwaitingApproval`|Datový typ Boolean|Zda předplatné čeká na schválení.|  
|`StartDate`|Datum a čas|Počáteční datum předplatného ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Datum a čas|Datum vypršení platnosti předplatného ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Datum a čas|Datum oznámení pro předplatné ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|řetězec|Primární klíč předplatného. Maximální délka je 256 znaků.|  
|`secondaryKey`|řetězec|Sekundární klíč předplatného. Maximální délka je 256 znaků.|  
|`CanBeRenewed`|Datový typ Boolean|Určuje, zda může být předplatné obnoveno aktuálním uživatelem.|  
|`HasExpired`|Datový typ Boolean|Zda platnost předplatného vypršela.|  
|`IsRejected`|Datový typ Boolean|Zda byl požadavek na odběr zamítnut.|  
|`CancelUrl`|řetězec|Relativní adresa URL pro zrušení odběru.|  
|`RenewUrl`|řetězec|Relativní adresa URL pro obnovení předplatného|  
  
##  <a name="SubscriptionSummary"></a>Souhrn předplatných  
 `subscription summary` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|řetězec|Identifikátor prostředku Jednoznačně identifikuje odběr v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}`, kde `{sid}` je identifikátor předplatného. Tato vlastnost je jen ke čtení.|  
|`DisplayName`|řetězec|Zobrazovaný název předplatného|  
  
##  <a name="UserAccountInfo"></a>Informace o uživatelském účtu  
 `user account info` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`FirstName`|řetězec|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`LastName`|řetězec|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`Email`|řetězec|E-mailovou adresu. Nesmí být prázdné a musí být v rámci instance služby jedinečné. Maximální délka je 254 znaků.|  
|`Password`|řetězec|Heslo k uživatelskému účtu.|  
|`NameIdentifier`|řetězec|Identifikátor účtu, který se shoduje s e-mailem uživatele.|  
|`ProviderName`|řetězec|Název zprostředkovatele ověřování.|  
|`IsBasicAccount`|Datový typ Boolean|True, pokud byl tento účet zaregistrován pomocí e-mailu a hesla; false, pokud byl účet zaregistrován pomocí poskytovatele.|  
  
##  <a name="UseSignIn"></a>Přihlášení uživatele  
 `user sign in` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Email`|řetězec|E-mailovou adresu. Nesmí být prázdné a musí být v rámci instance služby jedinečné. Maximální délka je 254 znaků.|  
|`Password`|řetězec|Heslo k uživatelskému účtu.|  
|`ReturnUrl`|řetězec|Adresa URL stránky, na kterou uživatel klikl na přihlášení|  
|`RememberMe`|Datový typ Boolean|Určuje, zda se mají ukládat informace o aktuálním uživateli.|  
|`RegistrationEnabled`|Datový typ Boolean|Zda je povolena registrace.|  
|`DelegationEnabled`|Datový typ Boolean|Zda je povoleno delegované přihlášení.|  
|`DelegationUrl`|řetězec|Adresa URL delegovaného přihlášení, pokud je povolená|  
|`SsoSignUpUrl`|řetězec|Adresa URL jednotného přihlašování pro uživatele, pokud je k dispozici|  
|`AuxServiceUrl`|řetězec|Pokud je aktuální uživatel správcem, jedná se o odkaz na instanci služby v Azure Portal.|  
|`Providers`|Kolekce entit [poskytovatele](#Provider)|Poskytovatelé ověřování pro tohoto uživatele.|  
|`UserRegistrationTerms`|řetězec|Podmínky, se kterými musí uživatel souhlasit, než se přihlásí.|  
|`UserRegistrationTermsEnabled`|Datový typ Boolean|Určuje, jestli jsou povolené výrazy.|  
  
##  <a name="UserSignUp"></a>Registrace uživatele  
 `user sign up` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|Datový typ Boolean|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`Password`|řetězec|Heslo k uživatelskému účtu.|  
|`PasswordVerdictLevel`|number|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`UserRegistrationTerms`|řetězec|Podmínky, se kterými musí uživatel souhlasit, než se přihlásí.|  
|`UserRegistrationTermsOptions`|number|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`ConsentAccepted`|Datový typ Boolean|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`Email`|řetězec|E-mailovou adresu. Nesmí být prázdné a musí být v rámci instance služby jedinečné. Maximální délka je 254 znaků.|  
|`FirstName`|řetězec|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`LastName`|řetězec|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`UserData`|řetězec|Hodnota, kterou používá ovládací prvek [registrace](api-management-page-controls.md#sign-up) .|  
|`NameIdentifier`|řetězec|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`ProviderName`|řetězec|Název zprostředkovatele ověřování.|

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
