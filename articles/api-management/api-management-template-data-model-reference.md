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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176535"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referenční informace k datovému modelu šablony Azure API Management
Toto téma popisuje entity a typy reprezentace pro běžné položky používané v datových modelech pro šablony portálu pro vývojáře v Azure API Management.  
  
 Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referenční informace

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
|`id`|string|Identifikátor prostředku Jedinečně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}`, kde `{id}` je identifikátor rozhraní API. Tato vlastnost je určena jen pro čtení.|  
|`name`|string|Název rozhraní API Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|string|Popis rozhraní API Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`serviceUrl`|string|Absolutní adresa URL back-end služby implementující toto rozhraní API.|  
|`path`|string|Relativní adresa URL jednoznačně identifikuje toto rozhraní API a všechny jeho cesty k prostředkům v rámci instance služby API Management. Připojí se k základní adrese URL koncového bodu rozhraní API zadané během vytváření instance služby za účelem vytvoření veřejné adresy URL pro toto rozhraní API.|  
|`protocols`|pole čísla|V této části najdete popis protokolů, které mohou být operace v tomto rozhraní API vyvolány. Povolené hodnoty jsou `1 - http` a `2 - https`, nebo obojí.|  
|`authenticationSettings`|[Nastavení ověřování autorizačního serveru](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekce nastavení ověřování zahrnutá v tomto rozhraní API|  
|`subscriptionKeyParameterNames`|object|Volitelná vlastnost, která se dá použít k zadání vlastních názvů pro parametry dotazu nebo hlavičky obsahující klíč předplatného Pokud je tato vlastnost přítomna, musí obsahovat alespoň jednu ze dvou následujících vlastností.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Přehled rozhraní API  
 `API summary` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|string|Identifikátor prostředku Jedinečně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}`, kde `{id}` je identifikátor rozhraní API. Tato vlastnost je určena jen pro čtení.|  
|`name`|string|Název rozhraní API Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|string|Popis rozhraní API Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
  
##  <a name="Application"></a>Použití  
 `application` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Jedinečný identifikátor aplikace|  
|`Title`|string|Název aplikace|  
|`Description`|string|Popis aplikace|  
|`Url`|IDENTIFIKÁTOR URI|Identifikátor URI pro aplikaci|  
|`Version`|string|Informace o verzi aplikace|  
|`Requirements`|string|Popis požadavků pro aplikaci.|  
|`State`|číslo|Aktuální stav aplikace<br /><br /> -0 – registrováno<br /><br /> -1-odesláno<br /><br /> -2 – Publikováno<br /><br /> -3 – zamítnuto<br /><br /> -4 – nepublikováno|  
|`RegistrationDate`|Datum a čas|Datum a čas, kdy byla aplikace zaregistrována.|  
|`CategoryId`|číslo|Kategorie aplikace (finance, zábava atd.)|  
|`DeveloperId`|string|Jedinečný identifikátor vývojáře, který aplikaci odeslal.|  
|`Attachments`|Kolekce entit [příloh](#Attachment)|Jakékoli přílohy pro aplikaci, jako jsou snímky obrazovky nebo ikony.|  
|`Icon`|[Upevněn](#Attachment)|Ikona pro aplikaci|  
  
##  <a name="Attachment"></a>Upevněn  
 `attachment` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`UniqueId`|string|Jedinečný identifikátor pro přílohu|  
|`Url`|string|Adresa URL prostředku|  
|`Type`|string|Typ přílohy|  
|`ContentType`|string|Typ média přílohy|  
  
##  <a name="Sample"></a>Ukázka kódu  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`title`|string|Název operace.|  
|`snippet`|string|Tato vlastnost je zastaralá a neměla by se používat.|  
|`brush`|string|Která vybarvení syntaxe kódu má být použita při zobrazení ukázky kódu. Povolené hodnoty jsou `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`a `csharp`.|  
|`template`|string|Název této šablony vzorového kódu.|  
|`body`|string|Zástupný symbol pro ukázkovou část kódu fragmentu.|  
|`method`|string|Metoda HTTP operace.|  
|`scheme`|string|Protokol, který se má použít pro požadavek operace.|  
|`path`|string|Cesta k operaci.|  
|`query`|string|Příklad řetězce dotazu s definovanými parametry|  
|`host`|string|Adresa URL brány API Management služby pro rozhraní API, které obsahuje tuto operaci.|  
|`headers`|Kolekce entit [hlaviček](#Header)|Hlavičky pro tuto operaci|  
|`parameters`|Kolekce entit [parametrů](#Parameter)|Parametry, které jsou definovány pro tuto operaci.|  
  
##  <a name="Comment"></a>Vytvořena  
 `API` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|číslo|ID komentáře|  
|`CommentText`|string|Tělo komentáře Může zahrnovat HTML.|  
|`DeveloperCompany`|string|Název společnosti pro vývojáře|  
|`PostedOn`|Datum a čas|Datum a čas, kdy byla Poznámka publikována|  
  
##  <a name="Issue"></a>Chybu  
 `issue` entita má následující vlastnosti.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Jedinečný identifikátor problému.|  
|`ApiID`|string|ID pro rozhraní API, pro které se tento problém nahlásil.|  
|`Title`|string|Název problému|  
|`Description`|string|Popis problému|  
|`SubscriptionDeveloperName`|string|Křestní jméno vývojáře, který nahlásil problém.|  
|`IssueState`|string|Aktuální stav problému. Možné hodnoty jsou navrhovány, otevřeny, uzavřeny.|  
|`ReportedOn`|Datum a čas|Datum a čas hlášení problému.|  
|`Comments`|Kolekce entit [komentářů](#Comment)|Komentáře k tomuto problému.|  
|`Attachments`|Kolekce entit [příloh](api-management-template-data-model-reference.md#Attachment)|Jakékoli přílohy k problému.|  
|`Services`|Kolekce entit [rozhraní API](#API) .|Rozhraní API, která se přihlásila k odběru uživatelem, který problém vyřešil.|  
  
##  <a name="Filtering"></a>Jakou  
 `filtering` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Pattern`|string|Aktuální hledaný výraz; nebo `null`, pokud neexistuje hledaný termín.|  
|`Placeholder`|string|Text, který se má zobrazit v poli hledání, pokud není zadán žádný hledaný termín.|  
  
##  <a name="Header"></a>Hlaviček  
 Tato část popisuje `parameter` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|string|Název parametru|  
|`description`|string|Popis parametru|  
|`value`|string|Hodnota hlavičky|  
|`typeName`|string|Datový typ hodnoty hlavičky|  
|`options`|string|Nastavení.|  
|`required`|Boolean|Určuje, zda je záhlaví vyžadováno.|  
|`readOnly`|Boolean|Určuje, zda je hlavička určena pouze pro čtení.|  
  
##  <a name="HTTPRequest"></a>Požadavek HTTP  
 Tato část popisuje `request` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`description`|string|Popis požadavku na operaci|  
|`headers`|pole entit [záhlaví](#Header)|Hlavičky požadavku.|  
|`parameters`|pole [parametru](#Parameter)|Kolekce parametrů požadavku operace.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce reprezentace požadavků operace|  
  
##  <a name="HTTPResponse"></a>Odpověď HTTP  
 Tato část popisuje `response` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`statusCode`|Kladné celé číslo|Stavový kód odezvy operace.|  
|`description`|string|Popis odezvy operace.|  
|`representations`|pole [reprezentace](#Representation)|Kolekce reprezentace odezvy operací|  
  
##  <a name="Operation"></a>NázevOperace  
 `operation` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`id`|string|Identifikátor prostředku Jednoznačně identifikuje operaci v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{aid}/operations/{id}`, kde `{aid}` je identifikátor rozhraní API a `{id}` je identifikátor operace. Tato vlastnost je určena jen pro čtení.|  
|`name`|string|Název operace Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`description`|string|Popis operace Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`scheme`|string|V této části najdete popis protokolů, které mohou být operace v tomto rozhraní API vyvolány. Povolené hodnoty jsou `http`, `https`nebo `http` i `https`.|  
|`uriTemplate`|string|Šablona relativní adresy URL identifikující cílový prostředek pro tuto operaci. Může obsahovat parametry. Příklad: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|Adresa URL API Management brány, která hostuje rozhraní API.|  
|`httpMethod`|string|Metoda HTTP operace.|  
|`request`|[Požadavek HTTP](#HTTPRequest)|Entita obsahující podrobnosti žádosti|  
|`responses`|pole [odpovědi HTTP](#HTTPResponse)|Pole entit s [odpovědí HTTP](#HTTPResponse) operace.|  
  
##  <a name="Menu"></a>Nabídka operace  
 `operation menu` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`ApiId`|string|ID aktuálního rozhraní API|  
|`CurrentOperationId`|string|ID aktuální operace|  
|`Action`|string|Typ nabídky|  
|`MenuItems`|Kolekce entit [položek nabídky operace](#MenuItem)|Operace pro aktuální rozhraní API.|  
  
##  <a name="MenuItem"></a>Položka nabídky operace  
 `operation menu item` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|ID operace|  
|`Title`|string|Popis operace.|  
|`HttpMethod`|string|Metoda HTTP operace.|  
  
##  <a name="Paging"></a>Přenosu  
 `paging` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Page`|číslo|Číslo aktuální stránky|  
|`PageSize`|číslo|Maximální výsledky, které mají být zobrazeny na jedné stránce.|  
|`TotalItemCount`|číslo|Počet položek, které se mají zobrazit|  
|`ShowAll`|Boolean|Určuje, zda se mají zobrazit všechny výsledky na jedné stránce.|  
|`PageCount`|číslo|Počet stránek výsledků.|  
  
##  <a name="Parameter"></a>Ukazatele  
 Tato část popisuje `parameter` reprezentaci.  
  
|Vlastnost|Typ|Popis|  
|--------------|-----------------|----------|  
|`name`|string|Název parametru|  
|`description`|string|Popis parametru|  
|`value`|string|Hodnota parametru.|  
|`options`|pole řetězce|Hodnoty definované pro hodnoty parametrů dotazu.|  
|`required`|Boolean|Určuje, jestli je parametr povinný, nebo ne.|  
|`kind`|číslo|Určuje, zda je tento parametr parametr cesty (1) nebo parametr QueryString (2).|  
|`typeName`|string|Typ parametru.|  
  
##  <a name="Product"></a>Produktu  
 `product` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Identifikátor prostředku Jednoznačně identifikuje produkt v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `products/{pid}`, kde `{pid}` je identifikátor produktu. Tato vlastnost je určena jen pro čtení.|  
|`Title`|string|Název produktu Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`Description`|string|Popis produktu Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`Terms`|string|Produktové podmínkami použití. Vývojářům, kteří se pokoušejí přihlásit k odběru produktu, se zobrazí a bude muset tyto podmínky přijmout, aby mohli dokončit proces předplatného.|  
|`ProductState`|číslo|Určuje, jestli je produkt publikovaný, nebo ne. Publikované produkty můžou využít vývojáři na portálu pro vývojáře. Nepublikované produkty jsou viditelné pouze správcům.<br /><br /> Povolené hodnoty pro stav produktu jsou:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|Boolean|Určuje, jestli uživatel může mít k tomuto produktu víc předplatných.|  
|`MultipleSubscriptionsCount`|číslo|Maximální počet předplatných pro tento produkt může mít uživatel ve stejnou dobu.|  
  
##  <a name="Provider"></a>Zprostředkovatele  
 `provider` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Properties`|slovník řetězců|Vlastnosti tohoto zprostředkovatele ověřování.|  
|`AuthenticationType`|string|Typ poskytovatele. (Azure Active Directory, přihlášení na Facebooku, účet Google, účet Microsoft, Twitter).|  
|`Caption`|string|Zobrazovaný název zprostředkovatele|  
  
##  <a name="Representation"></a>Obrázek  
 Tato část popisuje `representation`.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`contentType`|string|Určuje registrovaný nebo vlastní typ obsahu pro toto znázornění, například `application/xml`.|  
|`sample`|string|Příklad reprezentace.|  
  
##  <a name="Subscription"></a>Formě  
 `subscription` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Identifikátor prostředku Jednoznačně identifikuje odběr v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}`, kde `{sid}` je identifikátor předplatného. Tato vlastnost je určena jen pro čtení.|  
|`ProductId`|string|Identifikátor prostředku produktu odebíraného produktu. Hodnota je platná relativní adresa URL ve formátu `products/{pid}`, kde `{pid}` je identifikátor produktu.|  
|`ProductTitle`|string|Název produktu Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`ProductDescription`|string|Popis produktu Nesmí být prázdný. Může obsahovat značky formátování HTML. Maximální délka je 1000 znaků.|  
|`ProductDetailsUrl`|string|Relativní adresa URL s podrobnostmi o produktu|  
|`state`|string|Stav předplatného. Možné stavy:<br /><br /> - `0 - suspended` – předplatné je blokované a předplatitel nemůže volat žádná rozhraní API produktu.<br /><br /> - `1 - active` – předplatné je aktivní.<br /><br /> - `2 - expired` – předplatné dosáhlo data vypršení platnosti a bylo deaktivováno.<br /><br /> - `3 - submitted` – žádost o předplatné udělal vývojář, ale ještě nebyla schválená ani zamítnutá.<br /><br /> - `4 - rejected` – žádost o předplatné byla zamítnuta správcem.<br /><br /> - `5 - cancelled` – předplatné zrušila vývojář nebo správce.|  
|`DisplayName`|string|Zobrazovaný název předplatného|  
|`CreatedDate`|Hodnotu|Datum vytvoření odběru ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|Boolean|Zda může být předplatné zrušeno aktuálním uživatelem.|  
|`IsAwaitingApproval`|Boolean|Zda předplatné čeká na schválení.|  
|`StartDate`|Hodnotu|Počáteční datum předplatného ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Hodnotu|Datum vypršení platnosti předplatného ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Hodnotu|Datum oznámení pro předplatné ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|Primární klíč předplatného. Maximální délka je 256 znaků.|  
|`secondaryKey`|string|Sekundární klíč předplatného. Maximální délka je 256 znaků.|  
|`CanBeRenewed`|Boolean|Určuje, zda může být předplatné obnoveno aktuálním uživatelem.|  
|`HasExpired`|Boolean|Zda platnost předplatného vypršela.|  
|`IsRejected`|Boolean|Zda byl požadavek na odběr zamítnut.|  
|`CancelUrl`|string|Relativní adresa URL pro zrušení odběru.|  
|`RenewUrl`|string|Relativní adresa URL pro obnovení předplatného|  
  
##  <a name="SubscriptionSummary"></a>Souhrn předplatných  
 `subscription summary` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Id`|string|Identifikátor prostředku Jednoznačně identifikuje odběr v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}`, kde `{sid}` je identifikátor předplatného. Tato vlastnost je určena jen pro čtení.|  
|`DisplayName`|string|Zobrazovaný název předplatného|  
  
##  <a name="UserAccountInfo"></a>Informace o uživatelském účtu  
 `user account info` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`FirstName`|string|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`LastName`|string|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`Email`|string|E-mailovou adresu Nesmí být prázdné a musí být v rámci instance služby jedinečné. Maximální délka je 254 znaků.|  
|`Password`|string|Heslo k uživatelskému účtu.|  
|`NameIdentifier`|string|Identifikátor účtu, který se shoduje s e-mailem uživatele.|  
|`ProviderName`|string|Název zprostředkovatele ověřování.|  
|`IsBasicAccount`|Boolean|True, pokud byl tento účet zaregistrován pomocí e-mailu a hesla; false, pokud byl účet zaregistrován pomocí poskytovatele.|  
  
##  <a name="UseSignIn"></a>Přihlášení uživatele  
 `user sign in` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Email`|string|E-mailovou adresu Nesmí být prázdné a musí být v rámci instance služby jedinečné. Maximální délka je 254 znaků.|  
|`Password`|string|Heslo k uživatelskému účtu.|  
|`ReturnUrl`|string|Adresa URL stránky, na kterou uživatel klikl na přihlášení|  
|`RememberMe`|Boolean|Určuje, zda se mají ukládat informace o aktuálním uživateli.|  
|`RegistrationEnabled`|Boolean|Zda je povolena registrace.|  
|`DelegationEnabled`|Boolean|Zda je povoleno delegované přihlášení.|  
|`DelegationUrl`|string|Adresa URL delegovaného přihlášení, pokud je povolená|  
|`SsoSignUpUrl`|string|Adresa URL jednotného přihlašování pro uživatele, pokud je k dispozici|  
|`AuxServiceUrl`|string|Pokud je aktuální uživatel správcem, jedná se o odkaz na instanci služby v Azure Portal.|  
|`Providers`|Kolekce entit [poskytovatele](#Provider)|Poskytovatelé ověřování pro tohoto uživatele.|  
|`UserRegistrationTerms`|string|Podmínky, se kterými musí uživatel souhlasit, než se přihlásí.|  
|`UserRegistrationTermsEnabled`|Boolean|Určuje, jestli jsou povolené výrazy.|  
  
##  <a name="UserSignUp"></a>Registrace uživatele  
 `user sign up` entita má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|Boolean|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`Password`|string|Heslo k uživatelskému účtu.|  
|`PasswordVerdictLevel`|číslo|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`UserRegistrationTerms`|string|Podmínky, se kterými musí uživatel souhlasit, než se přihlásí.|  
|`UserRegistrationTermsOptions`|číslo|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`ConsentAccepted`|Boolean|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`Email`|string|E-mailovou adresu Nesmí být prázdné a musí být v rámci instance služby jedinečné. Maximální délka je 254 znaků.|  
|`FirstName`|string|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`LastName`|string|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|`UserData`|string|Hodnota, kterou používá ovládací prvek [registrace](api-management-page-controls.md#sign-up) .|  
|`NameIdentifier`|string|Hodnota, kterou používá ovládací prvek registrace [pro registraci](api-management-page-controls.md#sign-up)|  
|`ProviderName`|string|Název zprostředkovatele ověřování.|

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
