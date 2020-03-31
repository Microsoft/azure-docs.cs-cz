---
title: Prostředky šablony Azure API Management | Dokumenty společnosti Microsoft
description: Přečtěte si o typech prostředků, které jsou dostupné pro použití v šablonách portálu pro vývojáře ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249500"
---
# <a name="azure-api-management-template-resources"></a>Prostředky šablony Azure API Management
Azure API Management poskytuje následující typy prostředků pro použití v šablonách portálu pro vývojáře.  
  
-   [Prostředky řetězce](#strings)  
  
-   [Zdroje glyfů](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a>Prostředky řetězce  
 Správa rozhraní API poskytuje komplexní sadu prostředků řetězce pro použití na portálu pro vývojáře. Tyto prostředky jsou lokalizovány do všech jazyků podporovaných službou API Management. Výchozí sada šablon používá tyto prostředky pro záhlaví stránky, popisky a všechny konstantní řetězce, které jsou zobrazeny na portálu pro vývojáře. Chcete-li v šablonách použít prostředek řetězce, zadejte předponu řetězce prostředků následovanou názvem řetězce, jak je znázorněno v následujícím příkladu.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Následující příklad je ze šablony Seznamu produktů a zobrazí **produkty** v horní části stránky.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Jsou podporovány následující možnosti lokalizace:

| Národní prostředí    | Jazyk               |
|-----------|------------------------|
| en      | "Anglicky"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "to"      | "Italiano"             |
| "ja-JP"   | "中中中"                |
| "ko"      | "Věrní"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brazílie)"   |
| "pt-pt"   | "Português (Portugalsko)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中中(中中)"           |
| "zh-hant" | "中中(中)"           |

 V následujících tabulkách naleznete prostředky řetězce, které jsou k dispozici pro použití v šablonách portálu pro vývojáře. Název tabulky použijte jako předponu pro řetězcové prostředky v této tabulce.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [Řetězce aplikačních seznamů](#ApplicationListStrings)  
  
-   [Řetězce podrobností aplikace](#AppDetailsStrings)  
  
-   [Řetězce aplikací](#AppStrings)  
  
-   [Společné zdroje](#CommonResources)  
  
-   [Běžné řetězce](#CommonStrings)  
  
-   [Dokumentace](#Documentation)  
  
-   [Řetězce ErrorPageStrings](#ErrorPageStrings)  
  
-   [Řetězce problémů](#IssuesStrings)  
  
-   [Řetězce notfound](#NotFoundStrings)  
  
-   [Řetězce podrobností produktu](#ProductDetailsStrings)  
  
-   [Řetězce produktů](#ProductsStrings)  
  
-   [ZprostředkovatelInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [Řetězce signinstrings](#SigninStrings)  
  
-   [Řetězce registrace](#SignupStrings)  
  
-   [Řetězce odběrů](#SubscriptionListStrings)  
  
-   [Řetězce předplatného](#SubscriptionStrings)  
  
-   [Řetězce UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a>ApisStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|Název stránkyApis|Rozhraní API|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a>Řetězce podrobností aplikace  
  
|Name (Název)|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Náhled aplikace|  
|Záhlaví webových aplikacírequirements|Požadavky|  
|WebApplicationsScreenshotAlt|Snímek obrazovky|  
|WebApplicationsScreenshotsHeader|Snímky obrazovek|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a>Řetězce aplikačních seznamů  
  
|Name (Název)|Text|  
|----------|----------|  
|WebDevelopersAppOdstranitpotvrzení|Opravdu chcete odebrat aplikaci?|  
|WebDevelopersAppNotpublished WebDevelopersAppNotpublished WebDevelopersAppNotpublished WebDevelopers|Nezveřejněno|  
|WebdevelopersAppNotSubmitted|Neodesláno|  
|Hlavička kategorie WebDevelopersAppTable|Kategorie|  
|WebDevelopersAppTableNameHeader|Name (Název)|  
|Záhlaví _WebDevelopersAppTableStateHeader|Stav|  
|WebDevelopersEditLink|Upravit|  
|WebDevelopersRegisterAppLink|Registrace aplikace|  
|WebDevelopersRemoveLink|Odebrat|  
|WebDevelopersSubmitLink|Odeslat|  
|WebDevelopersYourApplicationsHeader|Vaše aplikace|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a>Řetězce aplikací  
  
|Name (Název)|Text|  
|----------|----------|  
|Záhlaví webových aplikací|Aplikace|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a>Společné zdroje  
  
|Name (Název)|Text|  
|----------|----------|  
|NoItemsChcete-li zobrazit|Nenašly se žádné výsledky.|  
|Obecnézpráva výjimky|Něco není v pořádku. Může to být dočasná závada nebo chyba. Opakujte akci.|  
|GeneralJsonExceptionMessage|Něco není v pořádku. Může to být dočasná závada nebo chyba. Načtěte stránku znovu a akci opakujte.|  
|PotvrzeníZpráva Neuložené změny|Existují některé neuložené změny. Opravdu chcete zrušit a zahodit změny?|  
|AzureActiveDirectory|Azure Active Directory|  
|Zpráva HttpLargeRequestMessage|Http Request Body je příliš velký.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a>Běžné řetězce  
  
|Name (Název)|Text|  
|----------|----------|  
|ButtonLabelCancel|Zrušit|  
|ButtonLabelSave|Uložit|  
|Obecnézpráva výjimky|Něco není v pořádku. Může to být dočasná závada nebo chyba. Opakujte akci.|  
|NoItemsChcete-li zobrazit|Nejsou k dispozici žádné položky, které by bylo možné zobrazit.|  
|PagerButtonLabelPrvní|První|  
|PagerButtonLabelLast|Poslední|  
|PagerButtonLabelNext|Další|  
|PagerButtonLabelPředchozí|Předchozí|  
|Popisek stránkyNaNOfM|Stránka {0} z {1}|  
|Heslo příliš krátké|Heslo je příliš krátké|  
|EmailasPassword|Nepoužívejte e-mail jako heslo|  
|PasswordSameAsUživatelské jméno|Heslo nesmí obsahovat vaše uživatelské jméno.|  
|PasswordTwoCharacterClasses|Použití různých tříd znaků|  
|PasswordTooManyOpakování|Příliš mnoho opakování|  
|PasswordSequenceFound|Vaše heslo obsahuje sekvence|  
|PagerLabelPageSize|Velikost stránky|  
|ClonaNačítání|Načítání...|  
|TablePlaceholderNothingToDisplay|Pro vybrané období a obor nejsou k dispozici žádná data.|  
|ButtonLabelZavřít|Zavřít|  
  
###  <a name="documentation"></a><a name="Documentation"></a>Dokumentace  
  
|Name (Název)|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessageMessage|Neplatná{0}hlavička '|  
|WebDocumentationInvalidRequestErrorMessage|Neplatná adresa URL požadavku|  
|TextboxLabelAccessToken|Přístupový token *|  
|DropdownOptionPrimaryKeyFormat|Primární{0}|  
|DropdownOptionSecondaryKeyFormat|Sekundární{0}|  
|WebDocumentationSubscriptionKeyText|Klíč předplatného|  
|WebDocumentationTemplatesPřidání záhlaví|Přidání požadovaných záhlaví protokolu HTTP|  
|WebDocumentationTemplatesZákladní vzorek|Ukázka základní autorizace|  
|WebDocumentationTemplatesCurlForBasicAuth|pro základní autorizaci: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Zadejte hodnoty parametrů cesty (zobrazených jako {...}), klíče předplatného a hodnot parametrů dotazu.|  
|WebDocumentationTemplatesKlíč pro vývojáře|Určení klíče předplatného|  
|WebDocumentationTemplatesJavaApache|Tato ukázka používá klienta Apache HTTP z HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesVolitelnéParams|Podle potřeby zadejte hodnoty pro volitelné parametry.|  
|WebDocumentationTemplatesPhpBalíček|Tato ukázka používá balíček HTTP_Request2. (další informace:https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Zadejte hodnoty parametrů cesty (zobrazené jako {...}) a v případě potřeby tělo požadavku.|  
|WebDocumentationTemplatesRequestBody|Zadat text požadavku|  
|WebDocumentationTemplatesRequiredParams|Určení hodnot pro následující požadované parametry|  
|WebDocumentationTemplatesValuesForPath|Zadejte hodnoty parametrů cesty (zobrazeno jako {...})|  
|OAuth2AuthorizationEndpointDescription|Koncový bod autorizace se používá k interakci s vlastníkem prostředku a získat udělení autorizace.|  
|Název koncového bodu oauth2authorization|Koncový bod autorizace|  
|OAuth2TokenEndpointPopis|Koncový bod tokenu se používá klientzískat přístupový token předložením jeho udělení autorizace nebo aktualizovat token.|  
|Název koncového bodu OAuth2Token|Koncový bod tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<\> p Klient iniciuje tok přesměrováním uživatelského agenta vlastníka prostředku na koncový bod autorizace.  Klient obsahuje identifikátor klienta, požadovaný obor, místní stav a identifikátor URI přesměrování, ke kterému autorizační server odešle uživatelského agenta zpět, jakmile je udělen (nebo odepřen přístup).     </p\> <\> p Autorizační server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a zjistí, zda vlastník prostředku udělí nebo zamítne žádost o přístup klienta.     </p\> <\> p Za předpokladu, že vlastník prostředku udělí přístup, autorizační server přesměruje uživatelského agenta zpět na klienta pomocí dříve poskytnutého identifikátoru URI přesměrování (v požadavku nebo při registraci klienta).  Identifikátor URI přesměrování obsahuje autorizační kód a libovolný místní stav poskytnutý klientem dříve.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Pokud uživatel odmítne žádost o přístup, pokud je požadavek neplatný, klient bude informován pomocí následujících parametrů přidaných k přesměrování: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Žádost o autorizaci|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<\> p Klientská aplikace musí odeslat uživatele do koncového bodu autorizace, aby mohla zahájit proces OAuth.          V koncovém bodě autorizace uživatel ověří a pak udělí nebo odepře přístup k aplikaci.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<\> p Za předpokladu, že vlastník prostředku udělí přístup, autorizační server přesměruje uživatelského agenta zpět na klienta pomocí dříve poskytnutého identifikátoru URI přesměrování (v požadavku nebo při registraci klienta).  Identifikátor URI přesměrování obsahuje autorizační kód a libovolný místní stav poskytnutý klientem dříve. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<\> p Klient požaduje přístupový token z koncového bodu tokenu autorizačního serveru zahrnutím autorizačního kódu přijatého v předchozím kroku.  Při provádění požadavku se klient ověřuje pomocí autorizačního serveru.  Klient obsahuje identifikátor URI přesměrování použitý k získání autorizačního kódu pro ověření. </p\> <\> p Autorizační server ověřuje klienta, ověřuje autorizační kód a zajišťuje, že přijatý identifikátor URI přesměrování odpovídá identifikátoru URI použitému k přesměrování klienta v kroku (C).  Pokud je platný, autorizační server odpoví zpět s přístupovým tokenem a volitelně obnovovacím tokenem. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<\> p Pokud se ověření klienta požadavku nezdařilo nebo je neplatné, autorizační server odpoví stavovým kódem HTTP 400 (Bad Request) (pokud není uvedeno jinak) a obsahuje následující parametry s odpovědí. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<\> p Klient provede požadavek na koncový bod tokenu odesláním následujících parametrů pomocí formátu "application/x-www-form-urlencoded" se kódováním znaků UTF-8 v těle entity požadavku HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<\> p Autorizační server vydává přístupový token a volitelný obnovovací token a vytvoří odpověď přidáním následujících parametrů do těla entity odpovědi HTTP se stavovým kódem 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<\> p Klient se ověřuje pomocí autorizačního serveru a požaduje přístupový token z koncového bodu tokenu. </p\> <\> p Autorizační server ověřuje klienta a pokud je platný, vydá přístupový token. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Pokud požadavek selhal při ověřování klienta nebo je neplatný, autorizační server odpoví stavovým kódem HTTP 400 (Bad Request) (pokud není uvedeno jinak) a obsahuje následující parametry s odpovědí. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p Klient provede požadavek na koncový bod tokenu přidáním následujících parametrů pomocí formátu "application/x-www-form-urlencoded" se kódováním znaků UTF-8 v těle entity požadavku HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Pokud je požadavek na přístupový token platný a autorizovaný, autorizační server vydá přístupový token a volitelný obnovovací token a vytvoří odpověď přidáním následujících parametrů do těla entity odpovědi HTTP se stavovým kódem 200 (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<\> p Klient iniciuje tok přesměrováním uživatelského agenta vlastníka prostředku na koncový bod autorizace.  Klient obsahuje identifikátor klienta, požadovaný obor, místní stav a identifikátor URI přesměrování, ke kterému autorizační server odešle uživatelského agenta zpět, jakmile je udělen (nebo odepřen přístup). </p\> <\> p Autorizační server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a zjistí, zda vlastník prostředku udělí nebo zamítne žádost o přístup klienta. </p\> <\> p Za předpokladu, že vlastník prostředku udělí přístup, autorizační server přesměruje uživatelského agenta zpět na klienta pomocí dříve poskytnutého identifikátoru URI přesměrování.  Identifikátor URI přesměrování obsahuje přístupový token v fragmentu identifikátoru URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Pokud vlastník prostředku odmítne žádost o přístup nebo pokud požadavek selže z jiných důvodů, než je chybějící nebo neplatný identifikátor URI přesměrování, autorizační server informuje klienta přidáním následujících parametrů do součásti fragmentu identifikátoru URI přesměrování pomocí formátu "application/x-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<\> p Klientská aplikace musí odeslat uživatele do koncového bodu autorizace, aby mohla zahájit proces OAuth.      V koncovém bodě autorizace uživatel ověří a pak udělí nebo odepře přístup k aplikaci. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<\> p Pokud vlastník prostředku udělí žádost o přístup, autorizační server vydá přístupový token a doručí jej klientovi přidáním následujících parametrů do součásti fragmentu identifikátoru URI přesměrování pomocí formátu "application/x-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Tok autorizačního kódu je optimalizován pro klienty schopné zachovat důvěrnost svých přihlašovacích údajů (např. aplikace webového serveru implementované pomocí PHP, Java, Pythonu, Ruby, ASP.NET atd.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Udělení autorizačního kódu|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Tok pověření klienta je vhodný v případech, kdy klient (vaše aplikace) požaduje přístup k chráněným prostředkům pod jeho kontrolou. Klient je považován za vlastníka prostředku, takže není vyžadována žádná interakce koncového uživatele.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Udělení pověření klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicitní tok je optimalizován pro klienty, kteří nejsou schopni zachovat důvěrnost svých přihlašovacích údajů, o nichž je známo, že provozují konkrétní identifikátor URI přesměrování. Tito klienti jsou obvykle implementováni v prohlížeči pomocí skriptovacího jazyka, jako je JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicitní grant|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Tok pověření vlastníka prostředku je vhodný v případech, kdy má vlastník prostředku vztah důvěryhodnosti s klientem (vaší aplikací), například operačním systémem zařízení nebo vysoce privilegovanou aplikací. Tento tok je vhodný pro klienty schopné získat pověření vlastníka prostředku (uživatelské jméno a heslo, obvykle pomocí interaktivního formuláře).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Udělení pověření vlastníka prostředku|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<\> p Vlastník prostředku poskytuje klientovi své uživatelské jméno a heslo. </p\> <\> p Klient požaduje přístupový token z koncového bodu tokenu autorizačního serveru zahrnutím pověření přijatých od vlastníka prostředku.  Při provádění požadavku se klient ověřuje pomocí autorizačního serveru. </p\> <\> p Autorizační server ověřuje klienta a ověřuje pověření vlastníka prostředků a pokud je platný, vydá přístupový token. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Pokud požadavek selhal při ověřování klienta nebo je neplatný, autorizační server odpoví stavovým kódem HTTP 400 (Bad Request) (pokud není uvedeno jinak) a obsahuje následující parametry s odpovědí. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p Klient provede požadavek na koncový bod tokenu přidáním následujících parametrů pomocí formátu "application/x-www-form-urlencoded" se kódováním znaků UTF-8 v těle entity požadavku HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Pokud je požadavek na přístupový token platný a autorizovaný, autorizační server vydá přístupový token a volitelný obnovovací token a vytvoří odpověď přidáním následujících parametrů do těla entity odpovědi HTTP se stavovým kódem 200 (OK). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Žádost o přístupový token|  
|OAuth2Step_AuthorizationRequest_Name|Žádost o autorizaci|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Požadované. Přístupový token vydaný autorizačním serverem.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Požadované. Přístupový token vydaný autorizačním serverem.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Požadované. Přístupový token vydaný autorizačním serverem.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Požadované. Přístupový token vydaný autorizačním serverem.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Požadované. Identifikátor klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|Požadováno, pokud klient není ověřování s autorizačním serverem.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Požadované. Identifikátor klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Požadované. Autorizační kód generovaný autorizačním serverem.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Požadované. Autorizační kód přijatý z autorizačního serveru.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Volitelné. Text ASCII čitelný pro člověka, který poskytuje další informace.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Volitelné. Text ASCII čitelný pro člověka, který poskytuje další informace.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Volitelné. Text ASCII čitelný pro člověka, který poskytuje další informace.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Volitelné. Text ASCII čitelný pro člověka, který poskytuje další informace.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Volitelné. Text ASCII čitelný pro člověka, který poskytuje další informace.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelnou pro člověka s informacemi o chybě.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelnou pro člověka s informacemi o chybě.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelnou pro člověka s informacemi o chybě.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelnou pro člověka s informacemi o chybě.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelnou pro člověka s informacemi o chybě.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Požadované. Jeden kód chyby ASCII z následujícího: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Požadované. Jeden kód chyby ASCII z následující: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Požadované. Jeden kód chyby ASCII z následující: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Požadované. Jeden kód chyby ASCII z následujícího: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Požadované. Jeden kód chyby ASCII z následující: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Doporučené. Životnost v sekundách přístupového tokenu.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Doporučené. Životnost v sekundách přístupového tokenu.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Doporučené. Životnost v sekundách přístupového tokenu.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Doporučené. Životnost v sekundách přístupového tokenu.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Požadované. Hodnota musí být nastavena na "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Požadované. Hodnota musí být nastavena na "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Požadované. Hodnota musí být nastavena na "heslo".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Požadované. Heslo vlastníka prostředku.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Volitelné. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|POŽADOVÁNO, pokud byl parametr "redirect_uri" zahrnut do žádosti o autorizaci a jejich hodnoty musí být identické.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Volitelné. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Volitelné. Obnovovací token, který lze použít k získání nových přístupových tokenů.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Volitelné. Obnovovací token, který lze použít k získání nových přístupových tokenů.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Volitelné. Obnovovací token, který lze použít k získání nových přístupových tokenů.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Požadované. Hodnota musí být nastavena na "kód".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Požadované. Hodnota musí být nastavena na "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Volitelné. Rozsah žádosti o přístup.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|VOLITELNÉ, pokud je totožný s rozsahem požadovaným klientem; v opačném případě REQUIRED.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Volitelné. Rozsah žádosti o přístup.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|VOLITELNÉ, pokud je totožný s rozsahem požadovaným klientem; v opačném případě REQUIRED.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Volitelné. Rozsah žádosti o přístup.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|VOLITELNÉ, pokud je totožný s rozsahem požadovaným klientem; v opačném případě REQUIRED.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Volitelné. Rozsah žádosti o přístup.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VOLITELNÉ, pokud je totožný s rozsahem požadovaným klientem; v opačném případě REQUIRED.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|POŽADOVÁNO, pokud byl parametr "state" přítomen v žádosti o autorizaci klienta.  Přesná hodnota přijatá od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Doporučené. Neprůhledná hodnota používaná klientem k udržení stavu mezi požadavkem a zpětným voláním.  Autorizační server obsahuje tuto hodnotu při přesměrování uživatelského agenta zpět na klienta.  Parametr by měl být použit pro prevenci padělání požadavků na příčce webu.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|POŽADOVÁNO, pokud byl parametr "state" přítomen v žádosti o autorizaci klienta.  Přesná hodnota přijatá od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|POŽADOVÁNO, pokud byl parametr "state" přítomen v žádosti o autorizaci klienta.  Přesná hodnota přijatá od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Doporučené. Neprůhledná hodnota používaná klientem k udržení stavu mezi požadavkem a zpětným voláním.  Autorizační server obsahuje tuto hodnotu při přesměrování uživatelského agenta zpět na klienta.  Parametr by měl být použit pro prevenci padělání požadavků na příčce webu.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|POŽADOVÁNO, pokud byl parametr "state" přítomen v žádosti o autorizaci klienta.  Přesná hodnota přijatá od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Požadované. Typ vydaného tokenu.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Požadované. Typ vydaného tokenu.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Požadované. Typ vydaného tokenu.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Požadované. Typ vydaného tokenu.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Požadované. Uživatelské jméno vlastníka prostředku.|  
|OAuth2UnsupportedTokenType|Typ tokenu '{0}' není podporován.|  
|OAuth2InvalidState|Neplatná odpověď z autorizačního serveru|  
|OAuth2GrantType_AuthorizationCode|Autorizační kód|  
|OAuth2GrantType_Implicit|Implicitní|  
|OAuth2GrantType_ClientCredentials|Přihlašovací údaje klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Heslo vlastníka prostředku|  
|Webdocumentation302Kód|302 Nalezeno|  
|Webdocumentation400Kód|400 (Chybný požadavek)|  
|OAuth2SendingMethod_AuthHeader|Hlavička autorizace|  
|OAuth2SendingMethod_QueryParam|Parametr dotazu|  
|OAuth2AuthorizationServerGeneralException|Při autorizaci přístupu přes{0}|  
|OAuth2AuthorizationServerCommunicationException|Nelze navázat připojení HTTP k autorizačnímu serveru nebo bylo neočekávaně uzavřeno.|  
|WebDocumentationOAuth2Zpráva obecné chyby|Došlo k neočekávané chybě.|  
|Výjimka AuthorizationServerCommunicationException|Došlo k výjimce komunikace autorizačního serveru. Obraťte se na správce.|  
|TextblockSubscriptionKeyHeaderDescription|Klíč předplatného, který poskytuje přístup k tomuto rozhraní API. Nalezeno ve vašem <profil href='/developer'\></a\>.|  
|TextblockOAuthHeaderDescription|Přístupový token OAuth 2.0\> {0} získaný\>z <i</i . Podporované typy grantů: <i\> {1}</i\>.|  
|TextblockContentTypeHeaderDescription|Typ média těla odeslaného do rozhraní API.|  
|ErrorMessageApiNotAccessible|Rozhraní API, které se pokoušíte volat, není v tuto chvíli přístupné. Obraťte se na vydavatele rozhraní API\><href="/issues" zde</a\>.|  
|ChybaMessageApiTimedout|Rozhraní API, které se pokoušíte volat trvá déle, než je obvyklé získat odpověď zpět. Obraťte se na vydavatele rozhraní API\><href="/issues" zde</a\>.|  
|BadRequestParameterExpected|"'{0}' parametr je očekáván"|  
|TooltipTextDoubleClickToSelectAll|Dvojitým kliknutím vyberte vše.|  
|TooltipTextHideRevealSecret|Zobrazit/skrýt|  
|ButtonLinkOpenConsole|Vyzkoušet|  
|SectionHeadingRequestBody|Text požadavku|  
|SectionHeadingRequestParameters|Parametry požadavku|  
|Adresa SectionHeadingRequestUrl|Adresa URL požadavku|  
|SectionHeadingResponse|Odpověď|  
|SectionHeadingRequestHeaders|Hlavičky požadavku|  
|FormLabelSubtextNepovinné|optional|  
|Ukázky kódů oddílů|Ukázky kódů|  
|TextblockOpenidConnectHeaderDescription|Token OpenID Connect ID\> {0} získaný z\><i</i . Podporované typy grantů: <i\> {1}</i\>.|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a>Řetězce ErrorPageStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|LinkLabelBack|Zpět|  
|LinkLabelHomePage|domovská stránka|  
|LinkLabelSendUsEmail|Pošlete nám e-mail|  
|Chyba_názvu_|Omlouváme se, došlo k potížím s obsluhou požadované stránky.|  
|TextblockPotentialCauseIntermittentIssue|To může být občasný problém s přístupem k datům, který je již pryč.|  
|TextblockPotentialCauseOldLink|Odkaz, na který jste klikli, může být starý a už nepřejděte na správné místo.|  
|TextblockPotentialCauseTechnicalProblem|Na naší straně může být technický problém.|  
|TextblockPotentialSolutionRefresh|Zkuste stránku aktualizovat.|  
|TextblockPotentialSolutionStartOver|Začněte znovu {0}od našeho .|  
|TextblockPotentialSolutionTryAgain|Přejděte {0} a vyzkoušejte akci, kterou jste provedli znovu.|  
|TextReportProblem|{0}popisující, co se pokazilo, a podíváme se na to co nejdříve.|  
|TitlePotentialCause|Potenciální příčina|  
|TitlePotentialSolution|Je to možná jen dočasný problém, pár věcí, které můžete vyzkoušet|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a>Řetězce problémů  
  
|Name (Název)|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problémy|  
|WebIssuesNoActiveSubscriptions|Nemáte žádné aktivní odběry. Chcete-li nahlásit problém, musíte se přihlásit k odběru produktu.|  
|WebIssuesNotSignin|Nejsi přihlášená. Prosím, {0} nahlásit problém nebo psát komentář.|  
|Tlačítko WebIssuesReportIssue|Problém sestavy|  
|WebIssuesSignin|přihlášení|  
|WebissuesStatusreportedby|Stav: {0} &#124; Hlášeno{1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a>Řetězce notfound  
  
|Name (Název)|Text|  
|----------|----------|  
|LinkLabelHomePage|domovská stránka|  
|LinkLabelSendUsEmail|pošlete nám e-mail|  
|Název stránky nebyl nalezen.|Omlouváme se, ale nemůžete najít stránku, kterou hledáte|  
|TextblockPotentialCauseMisspelledurl|Je možné, že jste adresu URL zadali chybně, pokud jste ji zadali.|  
|TextblockPotentialCauseOldLink|Odkaz, na který jste klikli, může být starý a už nepřejděte na správné místo.|  
|TextblockPotentialSolutionRetype|Zkuste adresu URL znovu zadat.|  
|TextblockPotentialSolutionStartOver|Začněte znovu {0}od našeho .|  
|TextReportProblem|{0}popisující, co se pokazilo, a podíváme se na to co nejdříve.|  
|TitlePotentialCause|Potenciální příčina|  
|TitlePotentialSolution|Potenciální řešení|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a>Řetězce podrobností produktu  
  
|Name (Název)|Text|  
|----------|----------|  
|Smlouva webproductsagreement|Přihlášením se k {0} odběru produktu souhlasím `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`s .|  
|WebProductsLegalTermsLink|Podmínky použití|  
|Tlačítko Odběr webových produktů|Odběr|  
|WebProductsUsageLimitsHeader|Limity použití|  
|Webové produktyYouAreNotSubscribed|Jste přihlášeni k odběru tohoto produktu.|  
|WebProductsYouRequestedSubscription|Požádali jste o předplatné tohoto produktu.|  
|ErrorYouNeedToAgreeWithLegalTerms|Než budete moci pokračovat, musíte souhlasit s podmínkami použití.|  
|ButtonLabelAddSubscription|Přidat předplatné|  
|Název_předplatného linklabelchange|Změnit|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|Máte {0} předplatná tohoto produktu:|  
|TextblockSingleSubscriptionsCount|Máte {0} předplatné tohoto produktu:|  
|TextblockSingleApisCount|Tento produkt {0} obsahuje rozhraní API:|  
|TextblockMultipleApisCount|Tento produkt {0} obsahuje api:|  
|TextblockHeaderSubscribe|Přihlásit se k odběru produktů|  
|TextblockSubscriptionDescription|Nové předplatné bude vytvořeno takto:|  
|TextblockSubscriptionLimitReached|Bylo dosaženo limitu předplatného.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a>Řetězce produktů  
  
|Name (Název)|Text|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a>ZprostředkovatelInfoStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|Textové poleExternalIdentitiesZakázáno|Přihlášení je v tuto chvíli zakázáno správci.|  
|TextboxExternalIdentitiesSigninInvitation|Případně se přihlaste pomocí|  
|Textové poleExterníidentitySigninPozvánkaPrimární|Přihlaste se pomocí:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a>SigninResources  
  
|Name (Název)|Text|  
|----------|----------|  
|PrincipalNotFound|Objekt zabezpečení nebyl nalezen nebo podpis je neplatný.|  
|ChybaSsoAuthenticationFailed|Ověření při přizpůsobovadě se nezdařilo.|  
|ErrorSsoAuthenticationFailedDetailed|Neplatný token nebo podpis nelze ověřit.|  
|ChybaSsoTokenNeplatná|Token připřijit jako správu a přichystání k moc je neplatný|  
|ValidationErrorSpecificEmailAlreadyExists|E-mail '{0}' již registrován|  
|Validační chybaZvláštní platnost|E-mail '{0}' je neplatný|  
|ValidationErrorPasswordInvalid|Heslo je neplatné. Opravte chyby a akci opakujte.|  
|Vlastnost příliš krátká|{0}je příliš krátká|  
|WebAuthenticationAddresserEmailEmailInvalidErrorMessage|Neplatná e-mailová adresa|  
|ValidationMessageNewPasswordConfirmationRequired|Potvrdit nové heslo|  
|OvěřeníErrorPasswordConfirmationRequired|Potvrzení prázdného hesla|  
|WebAuthenticationEmailChangeNotice|E-mail s potvrzením {0}změny je na cestě do . Prosím, postupujte podle pokynů v něm potvrdit svou novou e-mailovou adresu. Pokud e-mail nedorazí do vaší schránky v příštích několika minutách, zkontrolujte složku nevyžádané pošty.|  
|WebAuthenticationEmailChangeNoticeHeader|Vaše žádost o změnu e-mailu byla úspěšně zpracována.|  
|WebAuthenticationEmailChangeNoticeTitle|Požadovaná změna e-mailu|  
|WebAuthenticationEmailHasBeenRevertedNotice|E-mail již existuje. Požadavek byl vrácen zpět.|  
|ValidationErrorEmailAlreadyExists|E-mail již existuje.|  
|ValidationErrorErrorEmailNeplatný|Neplatná e-mailová adresa|  
|Textová poleLabelEmail|E-mail|  
|OvěřeníErrorEmailPožadováno|E-mail je povinný.|  
|WebAuthenticationErrorNoticeHeader|Chyba|  
|Zpráva WebAuthenticationFieldErrorErrorMessage|{0}musí být maximální délka{1}|  
|Textová poleLabelEmailFirstName|Jméno|  
|ValidationErrorFirstNameRequired|Je vyžadováno křestní jméno.|  
|Validační chybaFirstNameNeplatná|Neplatné křestní jméno|  
|NoticeInvalidInvitationToken|Upozorňujeme, že potvrzovací odkazy jsou platné pouze 48 hodin. Pokud jste stále v tomto časovém rámci, ujistěte se, že váš odkaz je správný. Pokud platnost odkazu vypršela, opakujte akci, kterou se pokoušíte potvrdit.|  
|NoticeHeaderInvalidInvitationToken|Neplatný token pozvánky|  
|NoticeTitleInvalidInvitationToken|Potvrzovací chyba|  
|WebAuthenticationLastNameInvalidErrorMessage Message|Neplatné příjmení|  
|Textové poleLabelEmailLastName|Příjmení|  
|ValidationErrorLastNameRequired|Je vyžadováno příjmení.|  
|Oznámení O vypršení platnosti odkazu WebAuthenticationLink|Platnost potvrzovacího odkazu, který vám byl zaslán, vypršela. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrVypršela platnost|Odkaz pro obnovení hesla je neplatný nebo vypršela jeho platnost.|  
|WebAuthenticationLinkExpiredNoticeTitle|Odkaz odeslán|  
|WebAuthenticationNewPasswordLabel|Nové heslo|  
|ValidaceMessageNewPasswordRequired|Je vyžadováno nové heslo.|  
|Textové poleLabelNotificationsSenderEmail|E-mail odesílatele oznámení|  
|Název_textového poleNázev_názvu|Název organizace|  
|WebAuthenticationOrganizationPožadovanErrorMessage|Název organizace je prázdný.|  
|WebAuthenticationPasswordChangedNotice|Heslo bylo úspěšně aktualizováno.|  
|WebAuthenticationPasswordChangedNoticeNázev|Heslo aktualizováno|  
|WebAuthenticationPasswordCompareErrorMessage|Hesla se neshodují|  
|WebAuthenticationPasswordConfirmLabel|Potvrzení hesla|  
|ValidationErrorPasswordInvalidDetailed|Heslo je příliš slabé.|  
|Webový popisek hesla|Heslo|  
|ValidaceErrorPasswordRequired|Je vyžadováno heslo.|  
|WebAuthenticationPasswordResetSendNotice|Změna hesla potvrzení e-mail {0}je na cestě do . Chcete-li pokračovat v procesu změny hesla, postupujte podle pokynů v e-mailu.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Požadavek na obnovení hesla byl úspěšně zpracován.|  
|WebAuthenticationPasswordResetSendNoticeTitle|Požadované obnovení hesla|  
|WebAuthenticationRequestNotFoundNotice|Požadavek nebyl nalezen.|  
|WebAuthenticationSenderEmailRequiredErrorMessageMessage|E-mail odesílatele oznámení je prázdný.|  
|Webový popisek podpisu hesla|Změnu potvrďte zadáním hesla|  
|WebAuthenticationSignupConfirmNotice|E-mail s potvrzením {0}registrace je\> na cestě do .<br /\> Prosím, postupujte podle pokynů v e-mailu pro aktivaci vašeho účtu.<br / Pokud e-mail nedorazí do vaší schránky během několika příštích minut, zkontrolujte prosím složku nevyžádané pošty.|  
|WebAuthenticationSignupConfirmNoticeHeader|Váš účet byl úspěšně vytvořen.|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|E-mail s potvrzením registrace byl odeslán znovu|  
|WebAuthenticationSignupConfirmNoticeTitle|Účet vytvořen|  
|Zpráva WebAuthenticationTokenRequiredErrorMessage|Token je prázdný.|  
|WebAuthenticationUserAlreadyRegisteredNotice|Zdá se, že uživatel s tímto e-mailem je již registrován v systému. Pokud jste zapomněli heslo, zkuste ho obnovit nebo kontaktovat náš tým podpory.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Uživatel již registrován|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Již zaregistrováno|  
|ButtonLabelChangePassword|Změna hesla|  
|ButtonLabelChangeAccountInfo|Změna informací o účtu|  
|ButtonLabelCloseAccount|Zavřít účet|  
|WebAuthenticationInvalidCaptchaErrorMessage|Zadaný text se neshoduje s textem na obrázku. Zkuste to prosím znovu.|  
|ValidationErrorCredentialsjsounem|E-mail nebo heslo je neplatné. Opravte chyby a akci opakujte.|  
|WebAuthenticationRequestIsNotValid|Požadavek není platný.|  
|WebAuthenticationUserisNotConfirm|Před přihlášením potvrďte svou registraci.|  
|WebAuthenticationInvalidEmailFormated|E-mail je neplatný:{0}|  
|WebAuthenticationUserNotFound|Uživatel nenalezen|  
|WebAuthenticationTenantNotRegistered|Váš účet patří klientovi Služby Azure Active Directory, který nemá oprávnění k přístupu na tento portál.|  
|Ověření na webu se nezdařilo.|Ověřování se nezdařilo.|  
|WebAuthenticationGooglePlusNotPovolená|Ověřování se nezdařilo. Pokud jste aplikaci autorizovali, obraťte se na správce a ujistěte se, že je ověřování Google správně nakonfigurováno.|  
|ValidationErrorAllowedTenantIsRequired|Je vyžadován povolený tenant.|  
|ValidationErrorTenantIsNotValid|Tenant služby Azure{0}Active Directory ' není platný.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Přihlášení pomocí {0} účtu|  
|WebAuthenticationUserLimitNotice|Tato služba dosáhla maximálního počtu povolených uživatelů. Prosím, `<a href="mailto:{0}"\>contact the administrator</a\>` upgrade jejich služby a re-povolit registraci uživatele.|  
|WebAuthenticationUserLimitNoticeHeader|Registrace uživatele zakázána.|  
|WebAuthenticationUserLimitNoticeTitle|Registrace uživatele zakázána.|  
|WebAuthenticationUserRegistrationDisabledNotice|Registrace uživatelů byla správcem zakázána. Přihlaste se pomocí externího zprostředkovatele identity.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registrace uživatele zakázána.|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registrace uživatele zakázána.|  
|WebAuthenticationSignupPendingConfirmationNotice|Než dokončíme vytvoření vašeho účtu, musíme ověřit vaši e-mailovou adresu. Poslali jsme e-mail {0}na adresu . Chcete-li aktivovat svůj účet, postupujte podle pokynů v e-mailu. Pokud e-mail nedorazí během několika příštích minut, zkontrolujte složku nevyžádané pošty.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Našli jsme nepotvrzený účet pro {0}e-mailovou adresu . K dokončení vytvoření vašeho účtu potřebujeme ověřit vaši e-mailovou adresu. Poslali jsme e-mail {0}na adresu . Chcete-li aktivovat svůj účet, postupujte podle pokynů v e-mailu. Pokud e-mail nedorazí během několika příštích minut, zkontrolujte složku nevyžádané pošty.|  
|WebAuthenticationSignupPotvrzeníTéměř hotovo|Téměř hotovo|  
|WebAuthenticationSignupPotvrzeníEmailOdeslání|Poslali jsme e-mail {0}na adresu . Chcete-li aktivovat svůj účet, postupujte podle pokynů v e-mailu. Pokud e-mail nedorazí během několika příštích minut, zkontrolujte složku nevyžádané pošty.|  
|WebAuthenticationEmailSentNotificationMessage|E-mail byl úspěšně odeslán na adresu{0}|  
|WebAuthenticationNoAadTenantconfigured|Pro službu není nakonfigurován žádný klient služby Azure Active Directory.|  
|Zaškrtávací políčkoLabelUserRegistrationTermsConsentRequired|Souhlasím s `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Přečtěte si prosím, zkontrolujte`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Podmínky použití|  
|OvěřeníMessageConsentNotAccepted|Než budete moci pokračovat, musíte souhlasit s podmínkami použití.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a>Řetězce signinstrings  
  
|Name (Název)|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Forgot your password?|  
|Správce webového ověřování|Pokud jste správce, musíte `<a href="{0}"\>here</a\>`se přihlásit .|  
|WebAuthenticationNotaMember|Not a member yet? `<a href="/signup"\>Sign up now</a\>`|  
|Webové ověřováníPamatovat|Pamatuj si mě na tomhle počítači.|  
|WebAuthenticationSigininWithPassword|Přihlaste se pomocí svého uživatelského jména a hesla|  
|Název_webového ověřování|Přihlášení|  
|WebAuthenticationSignUpNow|Zaregistrujte se|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a>Řetězce registrace  
  
|Name (Název)|Text|  
|----------|----------|  
|Registrace do záhlaví stránky|Registrace|  
|WebAuthenticationalreadyAMember|Jste již členem?|  
|WebAuthenticationCreateNewAccount|Vytvoření nového účtu správy rozhraní API|  
|WebAuthenticationSigninNyní|Přihlaste se nyní|  
|ButtonLabelSignup|Registrace|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a>Řetězce odběrů  
  
|Name (Název)|Text|  
|----------|----------|  
|PředplatnéStornopotvrzení|Opravdu chcete toto předplatné zrušit?|  
|PředplatnéRenewConfirmation|Opravdu chcete toto předplatné obnovit?|  
|WebDevelopersManageSubscriptions|Správa předplatných|  
|Primární klíč webových vývojářů|Primární klíč|  
|WebDevelopersRegenerateLink|Znovu vytvořit|  
|Sekundární klíč webových vývojářů|Sekundární klíč|  
|TlačítkoLabelShowKey|Zobrazit|  
|ButtonLabelRenewSubscription|Obnovit|  
|WebDevelopersSubscriptionRequested|Požadováno dne{0}|  
|WebDevelopersSubscriptionRequestedState|Požádáno|  
|WebDevelopersSubscriptionTableNameHeader|Name (Název)|  
|Záhlaví _WebDevelopersSubscriptionTableStateHeader|Stav|  
|WebDevelopersUsageStatisticsLink|Sestavy Analytics|  
|WebDevelopersYourSubscriptions|Vaše předplatná|  
|SubscriptionPropertyLabelRequestedDatum|Požadováno dne|  
|SubscriptionPropertyLabelDatum|Spuštěno|  
|Název_ _NameSubscription|Přejmenovat předplatné|  
|Název_názvu Název_propertyu subscriptionPropertyName|Název předplatného|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a>Řetězce předplatného  
  
|Name (Název)|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Chcete svůj účet uzavřít?|  
|Název stránkyProfil|Profil|  
|TlačítkoLabelHideKey|Skrýt|  
|ButtonLabelRegenerateKey|Znovu vytvořit|  
|InformationMessageKeyWasRegenerated|Opravdu chcete tento klíč znovu vygenerovat?|  
|TlačítkoLabelShowKey|Zobrazit|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a>Řetězce UpdateProfileStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizovat profil|  
|Název stránkyUpdateProfile|Aktualizovat informace o účtu|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a>Userprofile  
  
|Name (Název)|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Změna informací o účtu|  
|ButtonLabelChangePassword|Změna hesla|  
|ButtonLabelCloseAccount|Zavřít účet|  
|Textová poleLabelEmail|E-mail|  
|Textová poleLabelEmailFirstName|Jméno|  
|Textové poleLabelEmailLastName|Příjmení|  
|Textové poleLabelNotificationsSenderEmail|E-mail odesílatele oznámení|  
|Název_textového poleNázev_názvu|Název organizace|  
|SubscriptionStateActive|Aktivní|  
|SubscriptionStateCancelled|Cancelled|  
|SubscriptionStateExpired|Platnost vypršela|  
|SubscriptionStateRejected|Rejected|  
|SubscriptionStateRequested|Požádáno|  
|SubscriptionStatePozastavened|Dočasně blokován.|  
|Šablona DefaultSubscriptionNameTemplate|{0}(výchozí)|  
|SubscriptionNameTemplate|Přístup pro vývojáře #{0}|  
|Název_předplatnéhoLabelSubscriptionName|Název předplatného|  
|ValidaceMessageSubscriptionNameNameRequired|Název předplatného nemůže být prázdný.|  
|ApiManagementUserLimitReached|Tato služba dosáhla maximálního počtu povolených uživatelů. Upgradujte na vyšší cenovou úroveň.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a>Zdroje glyfů  
 Šablony portálu pro správu rozhraní API mohou používat glyfy z [glyfů z Bootstrapu](https://getbootstrap.com/components/#glyphicons). Tato sada glyfů obsahuje více než 250 glyfů ve formátu písma ze sady [Glyphicon](https://glyphicons.com/) Halflings. Chcete-li použít glyf z této sady, použijte následující syntaxi.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Úplný seznam glyfů naleznete [v tématu Glyphicons from Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](api-management-developer-portal-templates.md).
