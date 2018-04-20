---
title: Referenční model dat šablony Azure API Management | Microsoft Docs
description: Další informace o reprezentace entitu a typ pro běžné položky použít v modelech dat pro vývojáře šablony portálu v Azure API Management.
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
ms.openlocfilehash: 0f27b6b529c2591e37d48e3386190077fc8efc32
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="azure-api-management-template-data-model-reference"></a>Referenční model dat šablony Azure API Management
Toto téma popisuje reprezentace entitu a typ pro běžné položky použít v modelech dat pro vývojáře šablony portálu v Azure API Management.  
  
 Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [Rozhraní API](#API)  
-   [Souhrn rozhraní API](#APISummary)  
-   [Aplikace](#Application)  
-   [Přílohy](#Attachment)  
-   [Ukázka kódu](#Sample)  
-   [Komentář](#Comment)  
-   [Filtrování](#Filtering)  
-   [Záhlaví](#Header)  
-   [Požadavek HTTP](#HTTPRequest)  
-   [Odpověď HTTP](#HTTPResponse)  
-   [Problém](#Issue)  
-   [Operace](#Operation)  
-   [Operace nabídky](#Menu)  
-   [Operace položky nabídky](#MenuItem)  
-   [Stránkování](#Paging)  
-   [Parametr](#Parameter)  
-   [Produktu](#Product)  
-   [Poskytovatel](#Provider)  
-   [Reprezentace](#Representation)  
-   [Předplatné](#Subscription)  
-   [Předplatné souhrn](#SubscriptionSummary)  
-   [Informace o účtu uživatele](#UserAccountInfo)  
-   [Přihlášení uživatele](#UseSignIn)  
-   [Registrace uživatele](#UserSignUp)  
  
##  <a name="API"></a> ROZHRANÍ API  
 `API` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|id|řetězec|Identifikátor prostředku. Jednoznačně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}` kde `{id}` je identifikátor rozhraní API. Tato vlastnost je jen pro čtení.|  
|jméno|řetězec|Název rozhraní API. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|description|řetězec|Popis rozhraní API. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální délka je 1 000 znaků.|  
|serviceUrl|řetězec|Absolutní adresa URL back-end službu implementace toto rozhraní API.|  
|path|řetězec|Relativní adresa URL jedinečně identifikující toto rozhraní API a všechny jeho cesty prostředku v rámci instance služby API Management. Připojí se k rozhraní API koncový bod základní adresa URL zadaná při vytvoření instance služby a vytvořit veřejnou adresu URL pro toto rozhraní API.|  
|protokoly|pole čísla|Popisuje, na které protokoly může vyvolat operace v tomto rozhraní API. Povolené hodnoty jsou `1 - http` a `2 - https`, nebo obojí.|  
|authenticationSettings|[Nastavení ověřování serveru ověřování](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Kolekce nastavení ověřování, které jsou zahrnuté v tomto rozhraní API.|  
|subscriptionKeyParameterNames|objekt|Volitelná vlastnost, která umožňuje určit vlastní názvy parametrů dotazu nebo hlavičku obsahující klíč předplatného. Pokud tuto vlastnost, musí obsahovat alespoň jeden z následujících dvou vlastností.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Souhrn rozhraní API  
 `API summary` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|id|řetězec|Identifikátor prostředku. Jednoznačně identifikuje rozhraní API v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{id}` kde `{id}` je identifikátor rozhraní API. Tato vlastnost je jen pro čtení.|  
|jméno|řetězec|Název rozhraní API. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|description|řetězec|Popis rozhraní API. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální délka je 1 000 znaků.|  
  
##  <a name="Application"></a> Aplikace  
 `application` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ID|řetězec|Jedinečný identifikátor aplikace.|  
|Nadpis|řetězec|Název aplikace.|  
|Popis|řetězec|Popis aplikace.|  
|URL|URI|Identifikátor URI pro aplikaci.|  
|Verze|řetězec|Informace o verzi pro aplikaci.|  
|Požadavky|řetězec|Popis požadavky pro aplikaci.|  
|Stav|číslo|Aktuální stav aplikace.<br /><br /> -0 - zaregistrován<br /><br /> -1 - odeslání<br /><br /> -2 - publikována<br /><br /> -3 - odmítnut<br /><br /> -4 - Nepublikováno|  
|RegistrationDate|DateTime|Datum a čas, který byl zaregistrován aplikace.|  
|CategoryId|číslo|Kategorie aplikace (Finance, zábava atd.)|  
|DeveloperId|řetězec|Jedinečný identifikátor vývojáře, které odeslána aplikace.|  
|Přílohy|Kolekce [přílohy](#Attachment) entity.|Přílohy pro aplikaci, například snímky obrazovky nebo ikon.|  
|Ikona|[Přílohy](#Attachment)|Ikona pro danou aplikaci.|  
  
##  <a name="Attachment"></a> Přílohy  
 `attachment` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Jedinečné ID|řetězec|Jedinečný identifikátor pro přílohy.|  
|URL|řetězec|Adresa URL prostředku.|  
|Typ|řetězec|Typ přílohy.|  
|Typ obsahu|řetězec|Typ média přílohu.|  
  
##  <a name="Sample"></a> Ukázka kódu  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|název|řetězec|Název operace.|  
|fragment kódu|řetězec|Tato vlastnost je zastaralá a by se neměla používat.|  
|štětce|řetězec|Které šablony, který se má použít při zobrazení Ukázka kódu zvýrazňování syntaxe kódu. Povolené hodnoty jsou `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, a `csharp`.|  
|šablona|řetězec|Název této šablony ukázkový kód.|  
|hlavní část|řetězec|Zástupný symbol pro část ukázka fragmentu kódu.|  
|method|řetězec|Metoda HTTP operace.|  
|Schéma|řetězec|Protokol bude použit pro tento požadavek operation.|  
|path|řetězec|Cesta k operaci.|  
|query|řetězec|Příklad řetězce dotazu s definovaných parametrů.|  
|hostitel|řetězec|Adresa URL brány služby API Management pro rozhraní API, která obsahuje tuto operaci.|  
|hlavičky|Kolekce [záhlaví](#Header) entity.|Hlavičky pro tuto operaci.|  
|parameters|Kolekce [parametr](#Parameter) entity.|Parametry, které jsou definované pro tuto operaci.|  
  
##  <a name="Comment"></a> Komentář  
 `API` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ID|číslo|Id komentář.|  
|CommentText|řetězec|Text komentář. Může zahrnovat HTML.|  
|DeveloperCompany|řetězec|Název společnosti vývojář.|  
|PostedOn|DateTime|Datum a čas, který účtuje komentář.|  
  
##  <a name="Issue"></a> Problém  
 `issue` Entita, která má následující vlastnosti.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ID|řetězec|Jedinečný identifikátor pro tento problém.|  
|ApiID|řetězec|Id pro rozhraní API, pro který byla nahlášena tento problém.|  
|Nadpis|řetězec|Název problému.|  
|Popis|řetězec|Popis problému.|  
|SubscriptionDeveloperName|řetězec|Křestní jméno vývojáře, která použije v hlášení problému.|  
|IssueState|řetězec|Aktuální stav problému. Možné hodnoty jsou navržený, otevřeno, Uzavřeno.|  
|ReportedOn|DateTime|Datum a čas, který ohlásil problém.|  
|Komentáře|Kolekce [komentář](#Comment) entity.|Komentáře k tomuto problému.|  
|Přílohy|Kolekce [přílohy](api-management-template-data-model-reference.md#Attachment) entity.|Přílohy k problému.|  
|Služby|Kolekce [rozhraní API](#API) entity.|Rozhraní API předplatné uživatelem, která selhala problém.|  
  
##  <a name="Filtering"></a> Filtrování  
 `filtering` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Vzor|řetězec|Aktuální hledaný termín; nebo `null` Pokud neexistuje žádný hledaný termín.|  
|Zástupný symbol|řetězec|Text, který zobrazit do vyhledávacího pole, pokud neexistuje žádný hledaný termín, který je zadán.|  
  
##  <a name="Header"></a> Záhlaví  
 Tato část popisuje `parameter` reprezentace.  
  
|Vlastnost|Popis|Typ|  
|--------------|-----------------|----------|  
|jméno|řetězec|Název parametru.|  
|description|řetězec|Popis parametru.|  
|hodnota|řetězec|Hodnota hlavičky.|  
|TypeName|řetězec|Datový typ hodnoty hlavičky.|  
|Možnosti|řetězec|Možnosti.|  
|Požadované|Boolean|Jestli je požadované hlavičky.|  
|Jen pro čtení|Boolean|Jestli záhlaví je jen pro čtení.|  
  
##  <a name="HTTPRequest"></a> Požadavek HTTP  
 Tato část popisuje `request` reprezentace.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|description|řetězec|Popis žádost o operaci.|  
|hlavičky|pole [záhlaví](#Header) entity.|Hlavičky požadavku.|  
|parameters|pole [parametr](#Parameter)|Kolekce parametrů žádost o operaci.|  
|reprezentace|pole [reprezentace](#Representation)|Kolekce reprezentace žádost o operaci.|  
  
##  <a name="HTTPResponse"></a> Odpověď HTTP  
 Tato část popisuje `response` reprezentace.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|statusCode|Kladné celé číslo|Stavový kód odpovědi operaci.|  
|description|řetězec|Operace popis odpovědi.|  
|reprezentace|pole [reprezentace](#Representation)|Kolekce reprezentace odpověď operace.|  
  
##  <a name="Operation"></a> operace  
 `operation` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|id|řetězec|Identifikátor prostředku. Jednoznačně identifikuje operace v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `apis/{aid}/operations/{id}` kde `{aid}` je identifikátor rozhraní API a `{id}` je identifikátor operaci. Tato vlastnost je jen pro čtení.|  
|jméno|řetězec|Název operace. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|description|řetězec|Popis operace. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální délka je 1 000 znaků.|  
|Schéma|řetězec|Popisuje, na které protokoly může vyvolat operace v tomto rozhraní API. Povolené hodnoty jsou `http`, `https`, nebo obojí `http` a `https`.|  
|uriTemplate|řetězec|Relativní adresa URL Šablona identifikace cílový prostředek pro tuto operaci. Může obsahovat parametry. Příklad: `customers/{cid}/orders/{oid}/?date={date}`|  
|hostitel|řetězec|Adresu URL brány API Management, který je hostitelem rozhraní API.|  
|HttpMethod|řetězec|Metoda operaci HTTP.|  
|Požadavek|[Požadavek HTTP](#HTTPRequest)|Entita obsahující podrobnosti požadavku.|  
|odpovědi|pole [odpovědi HTTP](#HTTPResponse)|Pole operace [odpověď HTTP](#HTTPResponse) entity.|  
  
##  <a name="Menu"></a> Operace nabídky  
 `operation menu` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ApiId|řetězec|Id aktuální rozhraní API.|  
|CurrentOperationId|řetězec|Id aktuální operace.|  
|Akce|řetězec|Typ nabídky.|  
|Vlastnost MenuItems|Kolekce [položky nabídky operaci](#MenuItem) entity.|Operace pro aktuální rozhraní API.|  
  
##  <a name="MenuItem"></a> Operace položky nabídky  
 `operation menu item` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ID|řetězec|Id operace.|  
|Nadpis|řetězec|Popis operace.|  
|HttpMethod|řetězec|Metoda Http operace.|  
  
##  <a name="Paging"></a> Stránkování  
 `paging` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Stránka|číslo|Aktuální číslo stránky.|  
|PageSize|číslo|Maximální výsledky, který se má zobrazit na jedné stránce.|  
|TotalItemCount|číslo|Počet položek pro zobrazení.|  
|ShowAll|Boolean|Jestli se má zobrazit všechny výsledky na jedné stránce.|  
|PageCount|číslo|Počet stránek výsledků.|  
  
##  <a name="Parameter"></a> Parametr  
 Tato část popisuje `parameter` reprezentace.  
  
|Vlastnost|Popis|Typ|  
|--------------|-----------------|----------|  
|jméno|řetězec|Název parametru.|  
|description|řetězec|Popis parametru.|  
|hodnota|řetězec|Hodnota parametru.|  
|Možnosti|Pole řetězců|Hodnot definovaných pro hodnoty parametru dotazu.|  
|Požadované|Boolean|Určuje, zda parametr je povinný, nebo ne.|  
|Typ|číslo|Jestli je tento parametr parametr cesty (1), nebo parametr řetězce dotazu (2).|  
|TypeName|řetězec|Typ parametru.|  
  
##  <a name="Product"></a> Produktu  
 `product` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ID|řetězec|Identifikátor prostředku. Jednoznačně identifikuje produktu v aktuální instanci služby API Management. Hodnota je platná relativní adresa URL ve formátu `products/{pid}` kde `{pid}` je identifikátor produktu. Tato vlastnost je jen pro čtení.|  
|Nadpis|řetězec|Název produktu. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|Popis|řetězec|Popis produktu. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální délka je 1 000 znaků.|  
|Výrazy|řetězec|Produkt podmínky použití. Vývojáři pokusu o přihlášení k odběru produktu se zobrazí a můžou přijmout tyto podmínky před dokončením procesu předplatného.|  
|ProductState|číslo|Určuje, zda je produkt publikovaný nebo ne. Publikované produkty jsou zjistitelný vývojáři na portál pro vývojáře. Není publikována produkty jsou viditelné pouze správcům.<br /><br /> Povolené hodnoty pro stav produktu jsou:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|Boolean|Určuje, jestli uživatel může mít několik odběrů tohoto produktu ve stejnou dobu.|  
|MultipleSubscriptionsCount|číslo|Maximální počet odběrů tento produkt uživatel může mít ve stejnou dobu.|  
  
##  <a name="Provider"></a> Zprostředkovatel  
 `provider` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Vlastnosti|slovník řetězců.|Vlastnosti pro tohoto zprostředkovatele ověřování.|  
|authenticationType.|řetězec|Typ poskytovatele. (Azure Active Directory, Facebook přihlášení, účet Google, Microsoft Account, služby Twitter).|  
|Popisek|řetězec|Zobrazovaný název zprostředkovatele.|  
  
##  <a name="Representation"></a> Reprezentace  
 Tato část popisuje `representation`.  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Typ obsahu|řetězec|Určuje registrované nebo vlastní typ obsahu pro tento reprezentace, například `application/xml`.|  
|Ukázka|řetězec|Příkladem reprezentace.|  
  
##  <a name="Subscription"></a> Předplatné  
 `subscription` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ID|řetězec|Identifikátor prostředku. Jednoznačně identifikuje předplatného v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}` kde `{sid}` je identifikátor předplatného. Tato vlastnost je jen pro čtení.|  
|ID produktu|řetězec|Identifikátor prostředku produktu odebírané produktu. Hodnota je platná relativní adresa URL ve formátu `products/{pid}` kde `{pid}` je identifikátor produktu.|  
|ProductTitle|řetězec|Název produktu. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|Popisvýrobku|řetězec|Popis produktu. Nesmí být prázdný. Může zahrnovat formátování značky HTML. Maximální délka je 1 000 znaků.|  
|ProductDetailsUrl|řetězec|Relativní adresu URL na podrobnosti o produktu.|  
|state|řetězec|Stav odběru. Je možné stavy:<br /><br /> - `0 - suspended` – předplatného je zablokovaný a odběrateli nelze volat všechny rozhraní API produktu.<br /><br /> - `1 - active` – je předplatné aktivní.<br /><br /> - `2 - expired` – předplatné dosaženo datum vypršení platnosti a bylo deaktivováno.<br /><br /> - `3 - submitted` – žádosti o odběr byl proveden vývojáře, ale má ještě schválení nebo odmítnutí.<br /><br /> - `4 - rejected` – žádosti o odběr byl odepřen správcem.<br /><br /> - `5 - cancelled` – předplatné zrušil vývojáře nebo správce.|  
|displayName|řetězec|Zobrazovaný název odběru.|  
|Datum vytvoření|Data a času|Datum odběr byl vytvořen, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|Boolean|Jestli předplatné můžete zrušit podle aktuálního uživatele.|  
|IsAwaitingApproval|Boolean|Jestli předplatného čeká na schválení.|  
|Počátečním|Data a času|Počáteční datum pro předplatné, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|Datumvypršení platnosti|Data a času|Datum vypršení platnosti pro předplatné, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Data a času|Datum oznámení pro předplatné, ve formátu ISO 8601: `2014-06-24T16:25:00Z`.|  
|primaryKey|řetězec|Předplatné primární klíč. Maximální délka je 256 znaků.|  
|sekundární klíč|řetězec|Klíč sekundární předplatného. Maximální délka je 256 znaků.|  
|CanBeRenewed|Boolean|Jestli předplatné můžete obnovovat podle aktuálního uživatele.|  
|HasExpired|Boolean|Jestli vypršela platnost předplatného.|  
|IsRejected|Boolean|Jestli předplatné žádost byla odepřena.|  
|cancelUrl|řetězec|Relativní adresa Url zrušit předplatné.|  
|RenewUrl|řetězec|Adresa Url relativní k obnovení odběru.|  
  
##  <a name="SubscriptionSummary"></a> Předplatné souhrn  
 `subscription summary` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|ID|řetězec|Identifikátor prostředku. Jednoznačně identifikuje předplatného v rámci aktuální instance služby API Management. Hodnota je platná relativní adresa URL ve formátu `subscriptions/{sid}` kde `{sid}` je identifikátor předplatného. Tato vlastnost je jen pro čtení.|  
|displayName|řetězec|Zobrazovaný název odběru|  
  
##  <a name="UserAccountInfo"></a> Informace o účtu uživatele  
 `user account info` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|FirstName|řetězec|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|Příjmení|řetězec|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|Email|řetězec|E-mailovou adresu Nesmí být prázdný a musí být jedinečné v rámci instance služby. Maximální délka je 254 znaků.|  
|Heslo|řetězec|Heslo uživatelského účtu.|  
|NameIdentifier|řetězec|Identifikátor účtu stejný jako e-mail uživatele.|  
|ProviderName|řetězec|Název zprostředkovatele ověřování.|  
|IsBasicAccount|Boolean|Hodnota TRUE, pokud tento účet byl zaregistrován pomocí e-mailu a heslo; false, pokud účet byl zaregistrován pomocí zprostředkovatele.|  
  
##  <a name="UseSignIn"></a> Přihlášení uživatele  
 `user sign in` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Email|řetězec|E-mailovou adresu Nesmí být prázdný a musí být jedinečné v rámci instance služby. Maximální délka je 254 znaků.|  
|Heslo|řetězec|Heslo uživatelského účtu.|  
|ReturnUrl|řetězec|Adresu URL stránky, kde uživatel klepl přihlásit.|  
|Obdobou|Boolean|Jestli se má uložit informace o aktuálním uživateli.|  
|RegistrationEnabled|Boolean|Zda je povolena registrace.|  
|DelegationEnabled|Boolean|Jestli je povolené delegované přihlášení.|  
|DelegationUrl|řetězec|Delegované přihlašovací adresa url, pokud je povoleno.|  
|SsoSignUpUrl|řetězec|Jednotné přihlašování adresu URL pro uživatele, pokud je k dispozici.|  
|AuxServiceUrl|řetězec|Pokud má aktuální uživatel je správce, toto je odkaz na instanci služby na portálu Azure.|  
|Poskytovatelé|Kolekce [zprostředkovatele](#Provider) entity|Zprostředkovatelé ověřování pro tohoto uživatele.|  
|UserRegistrationTerms|řetězec|Podmínky, které uživatel musí vyjádřit souhlas s před přihlášením.|  
|UserRegistrationTermsEnabled|Boolean|Jestli jsou povolené podmínky.|  
  
##  <a name="UserSignUp"></a> Registrace uživatele  
 `user sign up` Entita, která má následující vlastnosti:  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|PasswordConfirm|Boolean|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)registrace ovládacího prvku.|  
|Heslo|řetězec|Heslo uživatelského účtu.|  
|PasswordVerdictLevel|číslo|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)registrace ovládacího prvku.|  
|UserRegistrationTerms|řetězec|Podmínky, které uživatel musí vyjádřit souhlas s před přihlášením.|  
|UserRegistrationTermsOptions|číslo|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)registrace ovládacího prvku.|  
|ConsentAccepted|Boolean|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)registrace ovládacího prvku.|  
|Email|řetězec|E-mailovou adresu Nesmí být prázdný a musí být jedinečné v rámci instance služby. Maximální délka je 254 znaků.|  
|FirstName|řetězec|Křestní jméno. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|Příjmení|řetězec|Příjmení. Nesmí být prázdný. Maximální délka je 100 znaků.|  
|UserData|řetězec|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up) ovládacího prvku.|  
|NameIdentifier|řetězec|Hodnota používaná metodou [registrace](api-management-page-controls.md#sign-up)registrace ovládacího prvku.|  
|ProviderName|řetězec|Název zprostředkovatele ověřování.|

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](api-management-developer-portal-templates.md).
