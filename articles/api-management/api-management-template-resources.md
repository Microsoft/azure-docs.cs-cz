---
title: Šablony prostředků Azure API Management | Microsoft Docs
description: Další informace o typech prostředků, které jsou k dispozici pro použití v šablonách portálu vývojáře ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 212e7ea7bb2ffea63c7ba210195df0da38aa8f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23835395"
---
# <a name="azure-api-management-template-resources"></a>Šablony prostředků Azure API Management
Azure API Management poskytuje následující typy prostředků pro použití v vývojář portálu šablony.  
  
-   [Řetězce prostředků](#strings)  
  
-   [Glyfy prostředky](#glyphs)  
  
##  <a name="strings"></a>Řetězce prostředků  
 API Management poskytuje komplexní sadu řetězcové prostředky pro použití v portálu pro vývojáře. Tyto prostředky jsou lokalizované do všech jazyků podporovaných službou API Management. Výchozí sada šablony použije tyto prostředky pro záhlaví stránky, popisky a všechny konstantní řetězce, které jsou zobrazeny v portálu pro vývojáře. Pokud chcete používat prostředek řetězce v šablonách, zadejte předpona řetězce prostředků, za nímž následuje název řetězce, jak je znázorněno v následujícím příkladu.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 V následujícím příkladu je ze seznamu šablony produktu a zobrazí **produkty** v horní části stránky.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Naleznete v následujících tabulkách pro řetězcové prostředky k dispozici pro použití v vaše šablony portálu pro vývojáře. Pomocí názvu tabulky jako předpona pro řetězcové prostředky v této tabulce.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentace](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|PageTitleApis|Rozhraní API|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Náhled aplikace|  
|WebApplicationsRequirementsHeader|Požadavky|  
|WebApplicationsScreenshotAlt|snímek obrazovky|  
|WebApplicationsScreenshotsHeader|Snímky obrazovky|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Opravdu chcete odebrat aplikaci?|  
|WebDevelopersAppNotPublished|Není publikována.|  
|WebDevelopersAppNotSubminted|Neodesláno|  
|WebDevelopersAppTableCategoryHeader|Kategorie|  
|WebDevelopersAppTableNameHeader|Name (Název)|  
|WebDevelopersAppTableStateHeader|Stav|  
|WebDevelopersEditLink|Upravit|  
|WebDevelopersRegisterAppLink|Registrace aplikace|  
|WebDevelopersRemoveLink|Odebrat|  
|WebDevelopersSubmitLink|Odeslat|  
|WebDevelopersYourApplicationsHeader|Vaše aplikace|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|WebApplicationsHeader|Aplikace|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Name (Název)|Text|  
|----------|----------|  
|NoItemsToDisplay|Nenašli jsme žádné výsledky.|  
|GeneralExceptionMessage|Něco není pravé. To může být k dočasné chybě nebo chyby. Zkuste to prosím znovu.|  
|GeneralJsonExceptionMessage|Něco není pravé. To může být k dočasné chybě nebo chyby. Načtěte stránku a zkuste to znovu.|  
|ConfirmationMessageUnsavedChanges|Existují některé neuložené změny. Opravdu že chcete zrušit a tyto změny zahodit?|  
|Azureactivedirectory selhala|Azure Active Directory|  
|HttpLargeRequestMessage|Http text žádosti příliš velký.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|ButtonLabelCancel|Zrušit|  
|ButtonLabelSave|Uložit|  
|GeneralExceptionMessage|Něco není pravé. To může být k dočasné chybě nebo chyby. Zkuste to prosím znovu.|  
|NoItemsToDisplay|Nejsou k dispozici žádné položky, které by bylo možné zobrazit.|  
|PagerButtonLabelFirst|První|  
|PagerButtonLabelLast|poslední|  
|PagerButtonLabelNext|Další|  
|PagerButtonLabelPrevious|Prev|  
|PagerLabelPageNOfM|Stránku {0} {1}|  
|PasswordTooShort|Heslo je moc krátké.|  
|EmailAsPassword|Nepoužívejte e-mailu jako heslo|  
|PasswordSameAsUserName|Heslo nesmí obsahovat vaše uživatelské jméno|  
|PasswordTwoCharacterClasses|Použití třídy různých znaků|  
|PasswordTooManyRepetitions|Příliš mnoho opakování|  
|PasswordSequenceFound|Heslo obsahuje pořadí|  
|PagerLabelPageSize|Velikost stránky|  
|CurtainLabelLoading|Načítání...|  
|TablePlaceholderNothingToDisplay|Neexistují žádná data pro vybrané období a oboru|  
|ButtonLabelClose|Zavřít|  
  
###  <a name="Documentation"></a>Dokumentace  
  
|Name (Název)|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Neplatná hlavička {0}.|  
|WebDocumentationInvalidRequestErrorMessage|Neplatný požadavek adresy URL|  
|TextboxLabelAccessToken|Přístupový token *|  
|DropdownOptionPrimaryKeyFormat|Primární – {0}|  
|DropdownOptionSecondaryKeyFormat|Sekundární-{0}|  
|WebDocumentationSubscriptionKeyText|Svůj klíč předplatného|  
|WebDocumentationTemplatesAddHeaders|Přidejte požadované hlavičky protokolu HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Ukázka základní ověřování|  
|WebDocumentationTemplatesCurlForBasicAuth|pro základní ověřování použijte:--uživatele {username}: {heslo}|  
|WebDocumentationTemplatesCurlValuesForPath|Zadejte hodnoty pro parametry cesty (zobrazené jako {...}), vašeho předplatného klíče a hodnoty pro parametry dotazu|  
|WebDocumentationTemplatesDeveloperKey|Zadejte svůj klíč předplatného|  
|WebDocumentationTemplatesJavaApache|Tato ukázka používá Apache HTTP klienta z komponenty HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Zadejte hodnoty pro volitelné parametry, podle potřeby|  
|WebDocumentationTemplatesPhpPackage|Tato ukázka používá balíček HTTP_Request2. (Další informace: http://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Zadejte hodnoty pro parametry cesty (zobrazené jako {...}) a text žádosti v případě potřeby|  
|WebDocumentationTemplatesRequestBody|Zadejte text žádosti|  
|WebDocumentationTemplatesRequiredParams|Zadejte hodnoty pro následující požadované parametry|  
|WebDocumentationTemplatesValuesForPath|Zadejte hodnoty pro parametry cesty (zobrazené jako {...})|  
|OAuth2AuthorizationEndpointDescription|Koncový bod autorizace se používá k interakci s vlastníka prostředku a získání udělení autorizace.|  
|OAuth2AuthorizationEndpointName|Koncový bod autorizace|  
|OAuth2TokenEndpointDescription|Koncový bod token slouží klient získat přístupový token prezentací jeho autorizace grant nebo aktualizovat token.|  
|OAuth2TokenEndpointName|Koncový bod tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> klient zahájí toku přesměrováním vlastníka prostředku uživatelského agenta pro koncový bod autorizace.  Klient zahrne jeho identifikátoru klienta, požadovaném oboru, místní stavu a identifikátor URI přesměrování, ke kterému serveru ověřování odešle uživatelského agenta zpět, když je přístup povolen (nebo je odepřen).     < /p\> < p\> serveru ověřování ověřuje vlastníka prostředku (prostřednictvím uživatelského agenta) a určuje, zda uděluje vlastníka prostředku, nebo odmítne žádost o přístup klienta.     < /p\> < p\> za předpokladu, že vlastník prostředku uděluje přístup, server ověřování přesměruje uživatelského agenta zpět do klienta pomocí zadaný identifikátor URI přesměrování starší (v požadavku nebo během registrace klienta).  Identifikátor URI přesměrování zahrnuje autorizační kód a žádný místní stav dříve zadaných klientem.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Pokud uživatel odepře přístup k žádosti o, pokud žádosti je neplatný, klient bude informováni pomocí následujících parametrů přidat k přesměrování: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Žádost o ověření|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientská aplikace musí poslat uživateli koncového bodu autorizace za účelem zahájení procesu OAuth.          Na koncový bod autorizace uživatel ověří a pak povoluje nebo zakazuje přístup k aplikaci.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> za předpokladu, že vlastník prostředku uděluje přístup, server ověřování přesměruje uživatelského agenta zpět do klienta pomocí zadaný identifikátor URI přesměrování starší (v požadavku nebo během registrace klienta).  Identifikátor URI přesměrování zahrnuje autorizační kód a žádný místní stav dříve zadaných klientem. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> klient požádá přístupový token ze serveru ověřování s koncový bod tokenu s zahrnutím autorizační kód přijaté v předchozím kroku.  Při vytváření požadavku, klient se ověří pomocí serveru ověřování.  Klient zahrne přesměrování, které URI používají k získání autorizační kód pro ověření. < /p\> < p\> serveru ověřování ověřuje klienta, ověří autorizační kód a zaručí, že přijal identifikátor URI přesměrování odpovídá identifikátoru URI používá přesměrování klienta v kroku (C).  Pokud je platný, server ověřování odpoví zpět přístupový token a volitelně token obnovení. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> Pokud žádost o ověření klienta se nezdařilo nebo je neplatný, server ověřování odpoví s kódem stavu HTTP 400 (Chybný požadavek) (Pokud není uvedeno jinak) a obsahuje tyto parametry s odpovědí. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> klient odešle požadavek na koncový bod token odesláním následující parametry ve tvaru "application/x--www-form-urlencoded" znak kódování UTF-8 v HTTP požadavku obsah entity. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> serveru ověřování problémy přístupový token a volitelné obnovovací token a vytvoří přidáním následující parametry do obsahu entity HTTP odpovědi s 200 (OK) stavový kód odpovědi. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> klienta ověří server ověřování a požadavky přístupového tokenu z koncového bodu token. < /p\> < p\> serveru ověřování ověřuje klienta a pokud je platná, vydá přístupový token. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Pokud žádosti se nezdařilo ověření klienta nebo je neplatný server ověřování reagovat s kódem stavu HTTP 400 (Chybný požadavek) (Pokud není uvedeno jinak) a obsahuje tyto parametry s odpovědí. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient odešle požadavek na koncový bod token přidáním následující parametry ve tvaru "application/x--www-form-urlencoded" znak kódování UTF-8 v HTTP požadavku obsah entity. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Pokud žádosti o token přístupu je platná a oprávnění, problémy přístupový token a volitelné obnovovací token serveru ověřování a vytvoří přidáním následující parametry do obsahu entity HTTP odpovědi s 200 (OK) stavový kód odpovědi. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> klient zahájí toku přesměrováním vlastníka prostředku '' s uživatelského agenta pro koncový bod autorizace.  Klient zahrne jeho identifikátoru klienta, požadovaném oboru, místní stavu a identifikátor URI přesměrování, ke kterému serveru ověřování odešle uživatelského agenta zpět, když je přístup povolen (nebo je odepřen). < /p\> < p\> serveru ověřování ověřuje vlastníka prostředku (prostřednictvím uživatelského agenta) a určuje, zda vlastníka prostředku povoluje nebo zakazuje klienta s s žádost o přístup. < /p\> < p\> za předpokladu, že vlastník prostředku uděluje přístup, server ověřování přesměruje uživatelského agenta zpět do klienta pomocí dříve zadaný identifikátor URI přesměrování.  Identifikátor URI přesměrování obsahuje přístupový token fragment identifikátoru URI. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Pokud vlastníka prostředku odmítne žádost o přístup nebo pokud se požadavek nezdaří z jiných důvodů než přesměrování chybí nebo je neplatný identifikátor URI, server ověřování informuje klienta přidáním následující parametry pro fragment součásti přesměrování URI ve tvaru "application/x--www-form-urlencoded". < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientská aplikace musí poslat uživateli koncového bodu autorizace za účelem zahájení procesu OAuth.      Na koncový bod autorizace uživatel ověří a pak povoluje nebo zakazuje přístup k aplikaci. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> vlastník prostředku udělí žádost o přístup, server ověřování vydá přístupový token a doručí jej klient přidáním následující parametry pro fragment součásti přesměrování URI ve tvaru "application/x--www-form-urlencoded". < /p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Tok autorizačního kódu je optimalizovaná pro klientů podporujících zachování důvěrnosti své přihlašovací údaje (například aplikací webového serveru implementovaná pomocí PHP, Java, Python, Ruby, ASP.NET, atd.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Udělení autorizačního kódu|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Tok přihlašovacích údajů klienta je vhodné v případech, kdy klient (aplikace) požaduje přístup k chráněným prostředkům pod její kontrolou. Klient se považuje za vlastník prostředku, tak nebude vyžadována žádná interakce s koncovým uživatelem.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Udělení pověření klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicitní tok je optimalizovaná pro klienti nepodporující zachování důvěrnosti přihlašovacích známé provoz konkrétní identifikátor URI přesměrování. Tito klienti jsou obvykle implementované v prohlížeči pomocí skriptovací jazyk, jako je JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicitní grant|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Tok přihlašovacích údajů heslo vlastníka prostředku je vhodné v případech, kdy vlastníka prostředku obsahuje vztah důvěryhodnosti s klientem (aplikace), například operační systém zařízení nebo aplikace pro vysoce privilegované. Tento tok je vhodná pro klienty získat přihlašovací údaje (uživatelské jméno a heslo, obvykle pomocí interaktivního formuláře) vlastníka prostředku.|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Udělení oprávnění hesla vlastníka prostředku|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> vlastníka prostředku poskytuje klientovi jeho uživatelské jméno a heslo. < /p\> < p\> klient požádá přístupový token ze serveru ověřování s koncový bod tokenu s zahrnutím přihlašovací údaje přijaté z vlastníka prostředku.  Při vytváření požadavku, klient se ověří pomocí serveru ověřování. < /p\> < p\> serveru ověřování ověřuje klienta a ověří pověření vlastníka prostředku a pokud je platná, vydá přístupový token. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Pokud žádosti se nezdařilo ověření klienta nebo je neplatný server ověřování reagovat s kódem stavu HTTP 400 (Chybný požadavek) (Pokud není uvedeno jinak) a obsahuje tyto parametry s odpovědí. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient odešle požadavek na koncový bod token přidáním následující parametry ve tvaru "application/x--www-form-urlencoded" znak kódování UTF-8 v HTTP požadavku obsah entity. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Pokud žádosti o token přístupu je platná a oprávnění, problémy přístupový token a volitelné obnovovací token serveru ověřování a vytvoří přidáním následující parametry do obsahu entity HTTP odpovědi s 200 (OK) stavový kód odpovědi. < /p\>|  
|OAuth2Step_AccessTokenRequest_Name|Žádosti o token přístupu|  
|OAuth2Step_AuthorizationRequest_Name|Žádost o ověření|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|VYŽADUJE SE. Přístupový token vydán serverem ověřování.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|VYŽADUJE SE. Přístupový token vydán serverem ověřování.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|VYŽADUJE SE. Přístupový token vydán serverem ověřování.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VYŽADUJE SE. Přístupový token vydán serverem ověřování.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|VYŽADUJE SE. Identifikátor klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|VYŽADUJE, pokud není klient ověřování pomocí serveru ověřování.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|VYŽADUJE SE. Identifikátor klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|VYŽADUJE SE. Autorizační kód, který je generovaný serverem ověřování.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|VYŽADUJE SE. Autorizační kód přijal od serveru ověřování.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|VOLITELNÝ PARAMETR. Čitelný text ASCII poskytuje další informace.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|VOLITELNÝ PARAMETR. Čitelný text ASCII poskytuje další informace.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|VOLITELNÝ PARAMETR. Čitelný text ASCII poskytuje další informace.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|VOLITELNÝ PARAMETR. Čitelný text ASCII poskytuje další informace.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VOLITELNÝ PARAMETR. Čitelný text ASCII poskytuje další informace.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|VOLITELNÝ PARAMETR. Identifikátor URI identifikující čitelná pro člověka webové stránky s informace o této chybě.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|VOLITELNÝ PARAMETR. Identifikátor URI identifikující čitelná pro člověka webové stránky s informace o této chybě.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|VOLITELNÝ PARAMETR. Identifikátor URI identifikující čitelná pro člověka webové stránky s informace o této chybě.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|VOLITELNÝ PARAMETR. Identifikátor URI identifikující čitelná pro člověka webové stránky s informace o této chybě.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VOLITELNÝ PARAMETR. Identifikátor URI identifikující čitelná pro člověka webové stránky s informace o této chybě.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|VYŽADUJE SE. Kód chyby na jednom ASCII z následujícího: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|VYŽADUJE SE. Kód chyby na jednom ASCII z následujícího: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|VYŽADUJE SE. Kód chyby na jednom ASCII z následujícího: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|VYŽADUJE SE. Kód chyby na jednom ASCII z následujícího: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VYŽADUJE SE. Kód chyby na jednom ASCII z následujícího: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|NEDOPORUČUJE. Doba platnosti v sekundách přístupový token.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|NEDOPORUČUJE. Doba platnosti v sekundách přístupový token.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|NEDOPORUČUJE. Doba platnosti v sekundách přístupový token.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|NEDOPORUČUJE. Doba platnosti v sekundách přístupový token.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|VYŽADUJE SE. Hodnota musí být nastavena na "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|VYŽADUJE SE. Hodnota musí být nastavena na "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|VYŽADUJE SE. Hodnota musí být nastavena na "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|VYŽADUJE SE. Heslo vlastníka prostředku.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|VOLITELNÝ PARAMETR. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|VYŽADUJE-li parametr "redirect_uri" byla součástí žádost o ověření a jejich hodnoty musí být identické.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|VOLITELNÝ PARAMETR. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|VOLITELNÝ PARAMETR. Token obnovení, který můžete použít k získání nové přístupové tokeny.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|VOLITELNÝ PARAMETR. Token obnovení, který můžete použít k získání nové přístupové tokeny.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VOLITELNÝ PARAMETR. Token obnovení, který můžete použít k získání nové přístupové tokeny.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|VYŽADUJE SE. Hodnota musí být nastavena na "kódu".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|VYŽADUJE SE. Hodnota musí být nastavena na "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|VOLITELNÝ PARAMETR. Rozsah žádost o přístup.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|VOLITELNÉ, pokud totožný s oborem vyžádané klientem; VYŽADUJE, jinak hodnota.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|VOLITELNÝ PARAMETR. Rozsah žádost o přístup.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|VOLITELNÉ, pokud totožný s oborem vyžádané klientem; VYŽADUJE, jinak hodnota.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|VOLITELNÝ PARAMETR. Rozsah žádost o přístup.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|VOLITELNÉ, pokud totožný s oborem vyžádané klientem; VYŽADUJE, jinak hodnota.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|VOLITELNÝ PARAMETR. Rozsah žádost o přístup.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VOLITELNÉ, pokud totožný s oborem vyžádané klientem; VYŽADUJE, jinak hodnota.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|VYŽADUJE, pokud parametr "stavu" byla v požadavku klienta autorizace.  Přesná hodnota přijatou od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|NEDOPORUČUJE. Neprůhledné hodnoty, která používá klienta pro uchování stavu mezi žádostí a zpětného volání.  Autorizace serveru zahrnuje tato hodnota při přesměrování uživatelského agenta zpět do klienta.  Parametr by MĚL použít brání padělání požadavku posílaného mezi weby.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|VYŽADUJE, pokud parametr "stavu" byla v požadavku klienta autorizace.  Přesná hodnota přijatou od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|VYŽADUJE, pokud parametr "stavu" byla v požadavku klienta autorizace.  Přesná hodnota přijatou od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|NEDOPORUČUJE. Neprůhledné hodnoty, která používá klienta pro uchování stavu mezi žádostí a zpětného volání.  Autorizace serveru zahrnuje tato hodnota při přesměrování uživatelského agenta zpět do klienta.  Parametr by MĚL použít brání padělání požadavku posílaného mezi weby.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|VYŽADUJE, pokud parametr "stavu" byla v požadavku klienta autorizace.  Přesná hodnota přijatou od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|VYŽADUJE SE. Typ vydávaných tokenů.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|VYŽADUJE SE. Typ vydávaných tokenů.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|VYŽADUJE SE. Typ vydávaných tokenů.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VYŽADUJE SE. Typ vydávaných tokenů.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|VYŽADUJE SE. Uživatelské jméno vlastníka prostředku.|  
|OAuth2UnsupportedTokenType|Typ tokenu {0}' není supporetd.|  
|OAuth2InvalidState|Neplatná odpověď ze serveru ověřování|  
|OAuth2GrantType_AuthorizationCode|Autorizační kód|  
|OAuth2GrantType_Implicit|Implicitní|  
|OAuth2GrantType_ClientCredentials|Pověření klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Heslo vlastníka prostředku|  
|WebDocumentation302Code|302 Found|  
|WebDocumentation400Code|400 (Chybný požadavek)|  
|OAuth2SendingMethod_AuthHeader|Autorizační hlavičky|  
|OAuth2SendingMethod_QueryParam|Parametr dotazu|  
|OAuth2AuthorizationServerGeneralException|Došlo k chybě při autorizaci přístupu prostřednictvím {0}|  
|OAuth2AuthorizationServerCommunicationException|Nelze navázat připojení HTTP k autorizaci serveru nebo neočekávaně bylo ukončeno.|  
|WebDocumentationOAuth2GeneralErrorMessage|Došlo k neočekávané chybě.|  
|AuthorizationServerCommunicationException|Došlo k výjimce komunikace serveru autorizace. Obraťte se na správce.|  
|TextblockSubscriptionKeyHeaderDescription|Klíč předplatného, který poskytuje přístup k toto rozhraní API. Najít v vaší < href = "/ vývojáře '\>profil < /a\>.|  
|TextblockOAuthHeaderDescription|Přístupový token OAuth 2.0 získané z < i\>{0} < /i\>. Podporované typy udělení: < i\>{1} < /i\>.|  
|TextblockContentTypeHeaderDescription|Typ média obsahu odeslaných do rozhraní API.|  
|ErrorMessageApiNotAccessible|V tuto chvíli není dostupné rozhraní API, kterou se pokoušíte volat. Obraťte se na vydavatele rozhraní API < href = "/ problémy"\>sem < /a\>.|  
|ErrorMessageApiTimedout|Rozhraní API, kterou se pokoušíte volat trvá déle než obvykle získat zpět. Obraťte se na vydavatele rozhraní API < href = "/ problémy"\>sem < /a\>.|  
|BadRequestParameterExpected|"očekává se parametr {0}."|  
|TooltipTextDoubleClickToSelectAll|Dvakrát klikněte na Vybrat všechny.|  
|TooltipTextHideRevealSecret|Zobrazit či skrýt|  
|ButtonLinkOpenConsole|Vyzkoušet|  
|SectionHeadingRequestBody|Text žádosti|  
|SectionHeadingRequestParameters|Parametry žádosti|  
|SectionHeadingRequestUrl|Adresa URL požadavku|  
|SectionHeadingResponse|Odpověď|  
|SectionHeadingRequestHeaders|Hlavičky požadavku|  
|FormLabelSubtextOptional|Volitelné|  
|SectionHeadingCodeSamples|Ukázky kódů|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect id token získaný < i\>{0} < /i\>. Podporované typy udělení: < i\>{1} < /i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|LinkLabelBack|zpět|  
|LinkLabelHomePage|Domovská stránka|  
|LinkLabelSendUsEmail|Pošlete nám e-mail|  
|PageTitleError|Bohužel došlo k potížím, které slouží k požadované stránce|  
|TextblockPotentialCauseIntermittentIssue|To může být problém přístup přerušované dat, který je již pryč.|  
|TextblockPotentialCauseOldLink|Platnost odkazu, který jste klikli na může být starý a není už přejděte na správné místo.|  
|TextblockPotentialCauseTechnicalProblem|Na naší straně může být k technickým potížím.|  
|TextblockPotentialSolutionRefresh|Zkuste aktualizovat stránku.|  
|TextblockPotentialSolutionStartOver|Začněte znovu z našich {0}.|  
|TextblockPotentialSolutionTryAgain|Přejděte {0} a opakujte akci, kterou můžete provést znovu.|  
|TextReportProblem|{0} popisující, co došlo k chybě a My bude vypadat na to, co nejrychleji můžeme.|  
|TitlePotentialCause|Možná příčina|  
|TitlePotentialSolution|Je pravděpodobně právě dočasný problém, několik postupů k vyzkoušení.|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problémy|  
|WebIssuesNoActiveSubscriptions|Nemáte žádné aktivní odběry. Musíte se k odběru produktu, chcete-li ohlásit problém.|  
|WebIssuesNotSignin|Nejste přihlášení. Prosím {0} ohlásit problém nebo odeslat komentář.|  
|WebIssuesReportIssueButton|Sestava problém|  
|WebIssuesSignIn|Přihlášení|  
|WebIssuesStatusReportedBy|Stav: {0} &#124; Hlášené {1}|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|LinkLabelHomePage|Domovská stránka|  
|LinkLabelSendUsEmail|Pošlete nám e-mail|  
|PageTitleNotFound|Je nám líto Nemůžeme najít stránku, kterou hledáte|  
|TextblockPotentialCauseMisspelledUrl|Možná je chybně adresu URL Pokud jste zadali v.|  
|TextblockPotentialCauseOldLink|Platnost odkazu, který jste klikli na může být starý a není už přejděte na správné místo.|  
|TextblockPotentialSolutionRetype|Zkuste znovu napsat adresu URL.|  
|TextblockPotentialSolutionStartOver|Začněte znovu z našich {0}.|  
|TextReportProblem|{0} popisující, co došlo k chybě a My bude vypadat na to, co nejrychleji můžeme.|  
|TitlePotentialCause|Možná příčina|  
|TitlePotentialSolution|Možné řešení|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|WebProductsAgreement|Se přihlásíte k odběru produktu {0}, souhlasím s `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Podmínky použití|  
|WebProductsSubscribeButton|Přihlásit odběr|  
|WebProductsUsageLimitsHeader|Omezení využití|  
|WebProductsYouAreNotSubscribed|Jsou přihlášení k odběru tohoto produktu.|  
|WebProductsYouRequestedSubscription|Požadujete odběr tohoto produktu.|  
|ErrorYouNeedtoAgreeWithLegalTerms|Abyste mohli pokračovat, musíte souhlasit s podmínkami použití.|  
|ButtonLabelAddSubscription|Přidat předplatné|  
|LinkLabelChangeSubscriptionName|změnit|  
|ButtonLabelConfirm|Potvrďte|  
|TextblockMultipleSubscriptionsCount|Máte předplatné {0}, tento produkt:|  
|TextblockSingleSubscriptionsCount|Máte předplatné {0} tohoto produktu:|  
|TextblockSingleApisCount|Tento produkt obsahuje {0} rozhraní API:|  
|TextblockMultipleApisCount|Tento produkt obsahuje {0} rozhraní API:|  
|TextblockHeaderSubscribe|Přihlášení k odběru produktu|  
|TextblockSubscriptionDescription|Nový odběr bude vytvořen následujícím způsobem:|  
|TextblockSubscriptionLimitReached|Dosažen limit odběry.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|V tuto chvíli je ve Správci zakázáno přihlášení.|  
|TextboxExternalIdentitiesSigninInvitation|Případně Přihlaste se pomocí|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Přihlaste se pomocí:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Name (Název)|Text|  
|----------|----------|  
|PrincipalNotFound|Objekt nebyl nalezen nebo pokud podpis je neplatný|  
|ErrorSsoAuthenticationFailed|Jednotné přihlašování ověřování se nezdařilo.|  
|ErrorSsoAuthenticationFailedDetailed|Neplatný token poskytnutý nebo podpis nelze ověřit.|  
|ErrorSsoTokenInvalid|Token jednotného přihlašování je neplatný|  
|ValidationErrorSpecificEmailAlreadyExists|E-mailu {0}' již zaregistrován.|  
|ValidationErrorSpecificEmailInvalid|E-mailu: {0} je neplatný|  
|ValidationErrorPasswordInvalid|Heslo je neplatné. Opravte chyby a zkuste to znovu.|  
|PropertyTooShort|{0} je příliš krátké.|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Neplatná e-mailová adresa.|  
|ValidationMessageNewPasswordConfirmationRequired|Potvrzení nového hesla|  
|ValidationErrorPasswordConfirmationRequired|Potvrďte, že heslo je prázdné.|  
|WebAuthenticationEmailChangeNotice|Změna potvrzení e-mailu je na cestě k {0}. Postupujte podle pokynů v něm k potvrzení vaší nové e-mailovou adresu. Pokud e-mailu nedorazil do vaší schránky v příštích několika minutách, zkontrolujte složku vašich nevyžádanou poštou.|  
|WebAuthenticationEmailChangeNoticeHeader|Vaše žádost o změnu e-mailu byl úspěšně zpracován|  
|WebAuthenticationEmailChangeNoticeTitle|Požadovaná změna e-mailu|  
|WebAuthenticationEmailHasBeenRevertedNotice|E-mail je již existuje. Žádost o byly vráceny zpět.|  
|ValidationErrorEmailAlreadyExists|E-mailu již existuje.|  
|ValidationErrorEmailInvalid|Neplatná e-mailová adresa|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|Vyžaduje se e-mailu.|  
|WebAuthenticationErrorNoticeHeader|Chyba|  
|WebAuthenticationFieldLengthErrorMessage|{0} musí být o maximální délce {1}|  
|TextboxLabelEmailFirstName|Jméno|  
|ValidationErrorFirstNameRequired|Jméno je povinné.|  
|ValidationErrorFirstNameInvalid|Neplatný křestní jméno|  
|NoticeInvalidInvitationToken|Upozorňujeme, že potvrzení odkazy jsou platné pouze 48 hodin. Pokud jste stále v rámci této časový rámec, Zkontrolujte prosím, že odkaz na vaši správná. Pokud vypršela platnost odkaz na vaši, opakujte akci, kterou se pokoušíte potvrďte.|  
|NoticeHeaderInvalidInvitationToken|Neplatný pozvánku tokenu|  
|NoticeTitleInvalidInvitationToken|Chyba potvrzení|  
|WebAuthenticationLastNameInvalidErrorMessage|Neplatný název poslední|  
|TextboxLabelEmailLastName|Příjmení|  
|ValidationErrorLastNameRequired|Příjmení je povinné.|  
|WebAuthenticationLinkExpiredNotice|Potvrzení odkaz vám zaslána vypršela platnost. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Odkaz na vaši resetování hesla je neplatná nebo vypršela její platnost.|  
|WebAuthenticationLinkExpiredNoticeTitle|Odkaz Odeslat|  
|WebAuthenticationNewPasswordLabel|Nové heslo|  
|ValidationMessageNewPasswordRequired|Nového hesla je povinné.|  
|TextboxLabelNotificationsSenderEmail|Oznámení odesílatele e-mailu|  
|TextboxLabelOrganizationName|Název organizace|  
|WebAuthenticationOrganizationRequiredErrorMessage|Název organizace je prázdný.|  
|WebAuthenticationPasswordChangedNotice|Heslo se úspěšně aktualizoval.|  
|WebAuthenticationPasswordChangedNoticeTitle|Heslo aktualizovat|  
|WebAuthenticationPasswordCompareErrorMessage|Hesla se neshodují.|  
|WebAuthenticationPasswordConfirmLabel|Potvrzení hesla|  
|ValidationErrorPasswordInvalidDetailed|Heslo je příliš slabé.|  
|WebAuthenticationPasswordLabel|Heslo|  
|ValidationErrorPasswordRequired|Je vyžadováno heslo.|  
|WebAuthenticationPasswordResetSendNotice|Změna hesla potvrzení e-mailu je na cestě k {0}. Postupujte podle pokynů v e-mailu, chcete-li pokračovat váš proces změny hesla.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Vaše žádost o resetování hesla byl úspěšně zpracován.|  
|WebAuthenticationPasswordResetSendNoticeTitle|Požádáno o obnovení hesla|  
|WebAuthenticationRequestNotFoundNotice|Nenašla se žádost|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Oznámení odesílatele e-mailu je prázdná|  
|WebAuthenticationSigninPasswordLabel|Potvrďte změny zadáním hesla|  
|WebAuthenticationSignupConfirmNotice|Registrace potvrzení e-mailu je směrem k {0}. < br /\> postupujte podle pokynů v e-mailu aktivovat svůj účet. < br /\> Pokud e-mailu nedorazil ve vaší doručené poště Další několika minut, zkontrolujte složku vašich nevyžádanou poštou.|  
|WebAuthenticationSignupConfirmNoticeHeader|Váš účet byl úspěšně vytvořen.|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Znovu odeslal registrační potvrzení e-mailu|  
|WebAuthenticationSignupConfirmNoticeTitle|Vytvoření účtu|  
|WebAuthenticationTokenRequiredErrorMessage|Token je prázdná|  
|WebAuthenticationUserAlreadyRegisteredNotice|Zdá se, že uživatel s Tento e-mail je již zaregistrován v systému. Pokud jste heslo zapomněli, zkuste obnovit nebo se obraťte na náš tým podpory.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Uživatel již zaregistrován.|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Zaregistrována.|  
|ButtonLabelChangePassword|Změnit heslo|  
|ButtonLabelChangeAccountInfo|Změnit informace o účtu|  
|ButtonLabelCloseAccount|Zavřít účet|  
|WebAuthenticationInvalidCaptchaErrorMessage|Zadaný text neodpovídá text na obrázku. Zkuste to prosím znovu.|  
|ValidationErrorCredentialsInvalid|E-mailu nebo heslo je neplatné. Opravte chyby a zkuste to znovu.|  
|WebAuthenticationRequestIsNotValid|Požadavek není platný|  
|WebAuthenticationUserIsNotConfirm|Registrace Zkontrolujte prosím před pokusem o přihlášení.|  
|WebAuthenticationInvalidEmailFormated|E-mailu je neplatná: {0}|  
|WebAuthenticationUserNotFound|Uživatel se nenašel.|  
|WebAuthenticationTenantNotRegistered|Váš účet patří do klienta Azure Active Directory, který nemá oprávnění pro přístup k tomuto portálu.|  
|WebAuthenticationAuthenticationFailed|Ověřování se nezdařilo.|  
|WebAuthenticationGooglePlusNotEnabled|Ověřování se nezdařilo. Pokud oprávnění aplikace pak prosím kontaktujte správce, abyste měli jistotu, že ověřování Google správně nakonfigurován.|  
|ValidationErrorAllowedTenantIsRequired|Je vyžadován povolené klienta|  
|ValidationErrorTenantIsNotValid|Klienta Azure Active Directory: {0} není platný.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Přihlaste se pomocí účtu {0}|  
|WebAuthenticationUserLimitNotice|Tato služba byl dosažen maximální počet povolených uživatelů. Prosím `<a href="mailto:{0}"\>contact the administrator</a\>` své služby a znovu povolit registraci uživatele.|  
|WebAuthenticationUserLimitNoticeHeader|Registrace uživatele byla zakázána.|  
|WebAuthenticationUserLimitNoticeTitle|Registrace uživatele byla zakázána.|  
|WebAuthenticationUserRegistrationDisabledNotice|Registrace uživatelů byla zakázána správcem. Přihlaste se pomocí zprostředkovatele externí identity.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registrace uživatele byla zakázána.|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registrace uživatele byla zakázána.|  
|WebAuthenticationSignupPendingConfirmationNotice|Předtím, než dokončíme vytvoření vašeho účtu potřebujeme ověřit vaši e-mailovou adresu. Poslali jsme e-mail na {0}. Postupujte podle pokynů v e-mailu aktivovat svůj účet. Pokud není e-mail dorazí do další několik minut, zkontrolujte složce nevyžádanou poštou.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Zjistili jsme nepotvrzeného účet pro e-mailovou adresu {0}. Vytvoření vašeho účtu potřebujeme ověřit vaši e-mailovou adresu. Poslali jsme e-mail na {0}. Postupujte podle pokynů v e-mailu aktivovat svůj účet. Pokud není e-mail dorazí do další několik minut, zkontrolujte složku vašich nevyžádanou poštou e-mailu|  
|WebAuthenticationSignupConfirmationAlmostDone|Skoro Hotovo|  
|WebAuthenticationSignupConfirmationEmailSent|Poslali jsme e-mail na {0}. Postupujte podle pokynů v e-mailu aktivovat svůj účet. Pokud není e-mail dorazí do další několik minut, zkontrolujte složce nevyžádanou poštou.|  
|WebAuthenticationEmailSentNotificationMessage|E-mailu úspěšně odeslat na {0}|  
|WebAuthenticationNoAadTenantConfigured|Žádná konfigurovaná pro službu klienta Azure Active Directory.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Souhlasím s `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Přečtěte si`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Podmínky použití|  
|ValidationMessageConsentNotAccepted|Abyste mohli pokračovat, musíte souhlasit s podmínkami použití.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Zapomněli jste heslo?|  
|WebAuthenticationIfAdministrator|Pokud jste správce musíte se přihlásit `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Není členem ještě? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Zapamatovat uživatele na tomto počítači|  
|WebAuthenticationSigininWithPassword|Přihlaste se pomocí uživatelského jména a hesla|  
|WebAuthenticationSigninTitle|Přihlášení|  
|WebAuthenticationSignUpNow|Zaregistrujte se|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|PageTitleSignup|Registrace|  
|WebAuthenticationAlreadyAMember|Již členem?|  
|WebAuthenticationCreateNewAccount|Vytvořit nový účet API Management|  
|WebAuthenticationSigninNow|Přihlaste se nyní|  
|ButtonLabelSignup|Registrace|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Opravdu chcete zrušit toto předplatné?|  
|SubscriptionRenewConfirmation|Opravdu chcete obnovit tento odběr?|  
|WebDevelopersManageSubscriptions|Správa předplatných|  
|WebDevelopersPrimaryKey|Primární klíč|  
|WebDevelopersRegenerateLink|Znovu vytvořit|  
|WebDevelopersSecondaryKey|Sekundární klíč|  
|ButtonLabelShowKey|Zobrazit|  
|ButtonLabelRenewSubscription|Obnovit|  
|WebDevelopersSubscriptionReqested|Požadovaný {0}|  
|WebDevelopersSubscriptionRequestedState|Požadovaný|  
|WebDevelopersSubscriptionTableNameHeader|Name (Název)|  
|WebDevelopersSubscriptionTableStateHeader|Stav|  
|WebDevelopersUsageStatisticsLink|Sestavy analýzy|  
|WebDevelopersYourSubscriptions|Předplatné|  
|SubscriptionPropertyLabelRequestedDate|Požadovaný|  
|SubscriptionPropertyLabelStartedDate|Bylo zahájeno|  
|PageTitleRenameSubscription|Přejmenujte předplatného|  
|SubscriptionPropertyLabelName|Název odběru|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Hledáte zavřít svůj účet?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Skrýt|  
|ButtonLabelRegenerateKey|Znovu vytvořit|  
|InformationMessageKeyWasRegenerated|Opravdu chcete znova vygenerovat tento klíč?|  
|ButtonLabelShowKey|Zobrazit|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Name (Název)|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizovat profil|  
|PageTitleUpdateProfile|Aktualizovat informace o účtu|  
  
###  <a name="UserProfile"></a>UserProfile  
  
|Name (Název)|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Změnit informace o účtu|  
|ButtonLabelChangePassword|Změnit heslo|  
|ButtonLabelCloseAccount|Zavřít účet|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Jméno|  
|TextboxLabelEmailLastName|Příjmení|  
|TextboxLabelNotificationsSenderEmail|Oznámení odesílatele e-mailu|  
|TextboxLabelOrganizationName|Název organizace|  
|SubscriptionStateActive|Aktivní|  
|SubscriptionStateCancelled|Zrušena|  
|SubscriptionStateExpired|Platnost|  
|SubscriptionStateRejected|Odmítnut|  
|SubscriptionStateRequested|Požadovaný|  
|SubscriptionStateSuspended|pozastaveno|  
|DefaultSubscriptionNameTemplate|{0} (výchozí)|  
|SubscriptionNameTemplate|Přístup vývojáře #{0}|  
|TextboxLabelSubscriptionName|Název odběru|  
|ValidationMessageSubscriptionNameRequired|Název odběru nemůže být prázdný.|  
|ApiManagementUserLimitReached|Tato služba byl dosažen maximální počet povolených uživatelů. Proveďte upgrade používat vyšší cenová úroveň.|  
  
##  <a name="glyphs"></a>Glyfy prostředky  
 Šablony portálu vývojáře API Management můžete použít glyfů z [Glyphicons z Bootstrap](http://getbootstrap.com/components/#glyphicons). Tato sada glyfů obsahuje více než 250 glyfů ve formátu písma z [Glyphicon](http://glyphicons.com/) Halflings nastavit. Pokud chcete používat glyf z této sady, použijte následující syntaxi.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Úplný seznam glyfů najdete v tématu [Glyphicons z Bootstrap](http://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](api-management-developer-portal-templates.md).
