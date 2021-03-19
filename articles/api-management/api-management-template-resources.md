---
title: Prostředky šablony Azure API Management | Microsoft Docs
description: Seznamte se s typy prostředků, které jsou k dispozici pro použití v šablonách portálu pro vývojáře v Azure API Management.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84689983"
---
# <a name="azure-api-management-template-resources"></a>Prostředky šablony Azure API Management
Azure API Management poskytuje následující typy prostředků pro použití v šablonách portálu pro vývojáře.  
  
-   [Prostředky řetězců](#strings)  
  
-   [Prostředky glyfů](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a> Prostředky řetězců  
 API Management poskytuje komplexní sadu prostředků řetězců pro použití na portálu pro vývojáře. Tyto prostředky jsou lokalizovány do všech jazyků podporovaných nástrojem API Management. Výchozí sada šablon používá tyto prostředky pro záhlaví a popisky stránek a libovolné konstantní řetězce, které se zobrazují na portálu pro vývojáře. Chcete-li použít prostředek řetězce v šablonách, zadejte předponu řetězce prostředku následovaný názvem řetězce, jak je znázorněno v následujícím příkladu.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Následující příklad je ze šablony seznamu produktů a zobrazuje **produkty** v horní části stránky.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Podporovány jsou následující možnosti lokalizace:

| Národní prostředí    | Jazyk               |
|-----------|------------------------|
| en      | Verzi              |
| cs      | Čeština              |
| &      | Deutsch              |
| jednomu      | "Español"              |
| FR      | Français             |
| hu      | Magyar               |
| její      | Italiano             |
| "ja-JP"   | "日本語"                |
| Ko      | "한국어"                |
| belgick      | Nederlands           |
| pl      | Polski               |
| pt-br   | "Português (Brazílii)"   |
| pt-PT   | "Português (Portugalsko)" |
| ru      | "Русский"              |
| činí      | Svenska              |
| recenzent      | "Türkçe"               |
| zh-Hans | "中文(简体)"           |
| zh-Hant | "中文(繁體)"           |

 Projděte si následující tabulky pro prostředky řetězců, které jsou k dispozici pro použití v šablonách portálu pro vývojáře. Jako předponu pro řetězcové prostředky v této tabulce použijte název tabulky.  
  
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
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a> ApisStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleApis|Rozhraní API|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Verze Preview aplikace|  
|WebApplicationsRequirementsHeader|Požadavky|  
|WebApplicationsScreenshotAlt|Snímek obrazovky|  
|WebApplicationsScreenshotsHeader|Screenshoty|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Opravdu chcete odebrat aplikaci?|  
|WebDevelopersAppNotPublished|Nepublikováno|  
|WebDevelopersAppNotSubmitted|Neodesláno|  
|WebDevelopersAppTableCategoryHeader|Kategorie|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|Stav|  
|WebDevelopersEditLink|Upravit|  
|WebDevelopersRegisterAppLink|Registrace aplikace|  
|WebDevelopersRemoveLink|Odebrat|  
|WebDevelopersSubmitLink|Odeslat|  
|WebDevelopersYourApplicationsHeader|Vaše aplikace|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a> AppStrings  
  
|Name|Text|  
|----------|----------|  
|WebApplicationsHeader|Aplikace|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a> CommonResources  
  
|Name|Text|  
|----------|----------|  
|NoItemsToDisplay|Nenašly se žádné výsledky.|  
|GeneralExceptionMessage|Něco není napravo. Může to být dočasný porucha nebo chyba. Zkuste to prosím znovu.|  
|GeneralJsonExceptionMessage|Něco není napravo. Může to být dočasný porucha nebo chyba. Načtěte prosím tuto stránku znovu a zkuste to znovu.|  
|ConfirmationMessageUnsavedChanges|Některé neuložené změny. Opravdu chcete změny zrušit a zahodit?|  
|Azureactivedirectory selhala|Azure Active Directory|  
|HttpLargeRequestMessage|Text požadavku HTTP je příliš velký.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a> CommonStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelCancel|Zrušit|  
|ButtonLabelSave|Uložit|  
|GeneralExceptionMessage|Něco není napravo. Může to být dočasný porucha nebo chyba. Zkuste to prosím znovu.|  
|NoItemsToDisplay|Neexistují žádné položky, které by bylo možné zobrazit.|  
|PagerButtonLabelFirst|První|  
|PagerButtonLabelLast|Poslední|  
|PagerButtonLabelNext|Další|  
|PagerButtonLabelPrevious|Prohlížen|  
|PagerLabelPageNOfM|Stránka {0} z {1}|  
|PasswordTooShort|Heslo je příliš krátké.|  
|EmailAsPassword|Nepoužívejte svůj e-mail jako heslo.|  
|PasswordSameAsUserName|Vaše heslo nemůže obsahovat vaše uživatelské jméno.|  
|PasswordTwoCharacterClasses|Použít různé třídy znaků|  
|PasswordTooManyRepetitions|Příliš mnoho opakování|  
|PasswordSequenceFound|Vaše heslo obsahuje posloupnosti.|  
|PagerLabelPageSize|Velikost stránky|  
|CurtainLabelLoading|Načítání...|  
|TablePlaceholderNothingToDisplay|Pro vybrané období a rozsah neexistují žádná data.|  
|ButtonLabelClose|Zavřít|  
  
###  <a name="documentation"></a><a name="Documentation"></a> Nápovědě  
  
|Name|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Neplatná hlavička {0}|  
|WebDocumentationInvalidRequestErrorMessage|Neplatná adresa URL požadavku|  
|TextboxLabelAccessToken|Přístupový token *|  
|DropdownOptionPrimaryKeyFormat|Primární{0}|  
|DropdownOptionSecondaryKeyFormat|Sekundární{0}|  
|WebDocumentationSubscriptionKeyText|Váš klíč předplatného|  
|WebDocumentationTemplatesAddHeaders|Přidat požadované hlavičky HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Ukázka základního ověřování|  
|WebDocumentationTemplatesCurlForBasicAuth|pro základní autorizaci použijte:--User {username}: {Password}|  
|WebDocumentationTemplatesCurlValuesForPath|Zadejte hodnoty parametrů cesty (zobrazené jako {...}), klíč předplatného a hodnoty pro parametry dotazu.|  
|WebDocumentationTemplatesDeveloperKey|Zadat klíč předplatného|  
|WebDocumentationTemplatesJavaApache|Tato ukázka používá klienta Apache HTTP ze součástí HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Zadejte hodnoty pro volitelné parametry podle potřeby.|  
|WebDocumentationTemplatesPhpPackage|Tato ukázka používá balíček HTTP_Request2. (Další informace: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Zadejte hodnoty parametrů cesty (zobrazené jako {...}) a v případě potřeby text žádosti.|  
|WebDocumentationTemplatesRequestBody|Zadat text žádosti|  
|WebDocumentationTemplatesRequiredParams|Zadejte hodnoty pro následující požadované parametry.|  
|WebDocumentationTemplatesValuesForPath|Zadejte hodnoty parametrů cesty (zobrazené jako {...}).|  
|OAuth2AuthorizationEndpointDescription|Koncový bod autorizace se používá k interakci s vlastníkem prostředku a získání udělení autorizace.|  
|OAuth2AuthorizationEndpointName|Koncový bod autorizace|  
|OAuth2TokenEndpointDescription|Koncový bod tokenu používá klient k získání přístupového tokenu tím, že prezentuje jeho autorizační udělení nebo obnovovací token.|  
|OAuth2TokenEndpointName|Koncový bod tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p \>         klient zahajuje tok tím, že na koncový bod autorizace nasměruje uživatelského agenta vlastníka prostředku.  Klient obsahuje identifikátor identifikátoru klienta, požadovaný rozsah, místní stav a identifikátor URI pro přesměrování, ke kterému autorizačnímu serveru pošle agenta uživatele zpět, jakmile bude udělen přístup (nebo odepřen).     </p \>     <p \>         autorizační Server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a určí, jestli vlastník prostředku udělí nebo odmítne žádost o přístup klienta.     </p \>     <p \>         za předpokladu, že vlastník prostředku udělí přístup, autorizační server přesměruje agenta uživatele zpět na klienta pomocí identifikátoru URI přesměrování uvedeného výše (v žádosti nebo během registrace klienta).  Identifikátor URI pro přesměrování zahrnuje autorizační kód a jakýkoliv místní stav poskytnutý klientem.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     Pokud uživatel žádost o přístup zamítne, pokud je žádost neplatná, bude klient informován pomocí následujících parametrů přidaných do přesměrování: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Žádost o autorizaci|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p \>         : klientská aplikace musí odeslat uživatele do koncového bodu autorizace, aby mohl zahájit proces OAuth.          Na koncovém bodu autorizace se uživatel ověří a pak udělí nebo odepře přístup k aplikaci.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     za předpokladu, že vlastník prostředků udělí přístup, přesměruje autorizační server uživatele agenta zpátky na klienta pomocí identifikátoru URI přesměrování, který jste zadali dříve (v žádosti nebo během registrace klienta).  Identifikátor URI pro přesměrování zahrnuje autorizační kód a jakýkoliv místní stav poskytnutý klientem. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p \>  klient požaduje přístupový token z koncového bodu tokenu autorizačního serveru tím, že zahrne autorizační kód přijatý v předchozím kroku.  Při vytváření žádosti se klient ověřuje pomocí autorizačního serveru.  Klient obsahuje identifikátor URI pro přesměrování, který se používá k získání autorizačního kódu pro ověřování. </p \> <p \>     ověří server klienta, ověří autorizační kód a zaručí, že přijatý identifikátor URI pro přesměrování odpovídá identifikátoru URI použitému k přesměrování klienta v kroku (C).  Pokud je to v platnosti, autorizační Server odpoví pomocí přístupového tokenu a volitelně také obnovovacím tokenem. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p \>     Pokud se ověření klienta požadavku nezdařilo nebo je neplatné, autorizační Server odpoví stavovým kódem HTTP 400 (špatný požadavek) (Pokud není uvedeno jinak) a obsahuje následující parametry s odpovědí. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p \>   klient vytvoří požadavek na koncový bod tokenu odesláním následujících parametrů pomocí formátu "application/x-www-form-urlencoded" s kódováním znaků UTF-8 v těle entity požadavku HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p \>  autorizační server vydá přístupový token a volitelný obnovovací token a vytvoří odpověď tím, že do těla entity odpovědi HTTP přidá následující parametry, které mají stavový kód 200 (ok). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p \>  se klient ověřuje pomocí autorizačního serveru a požádá o přístupový token z koncového bodu tokenu. </p \> <p \>  autorizační Server ověří klienta, a pokud je platný, vydá přístupový token. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>     , pokud se požadavek nezdařil při ověřování klienta nebo je neplatný, autorizační Server odpoví stavovým kódem HTTP 400 (špatný požadavek) (Pokud není uvedeno jinak) a obsahuje následující parametry s odpovědí. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>   klient vytvoří požadavek na koncový bod tokenu přidáním následujících parametrů pomocí formátu "application/x-www-form-urlencoded" s kódováním znaků UTF-8 v těle entity požadavku HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>  Pokud je požadavek přístupového tokenu platný a autorizovaný, autorizační server vydá přístupový token a volitelný obnovovací token a vytvoří odpověď přidáním následujících parametrů do těla zprávy odpovědi HTTP pomocí 200 (ok). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p \>   klient zahajuje tok tím, že na koncový bod autorizace nasměruje uživatelského agenta vlastníka prostředku.  Klient obsahuje identifikátor identifikátoru klienta, požadovaný rozsah, místní stav a identifikátor URI pro přesměrování, ke kterému autorizačnímu serveru pošle agenta uživatele zpět, jakmile bude udělen přístup (nebo odepřen). </p \> <p \>        autorizační Server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a určí, jestli vlastník prostředku udělí nebo odmítne žádost o přístup klienta. </p \> <p \>        za předpokladu, že vlastník prostředku udělí přístup, autorizační server přesměruje agenta uživatele zpět na klienta pomocí výše uvedeného identifikátoru URI pro přesměrování.  Identifikátor URI pro přesměrování zahrnuje přístupový token v fragmentu identifikátoru URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     Pokud vlastník prostředku odmítne žádost o přístup nebo pokud se požadavek nezdařil z jiných důvodů než chybějící nebo neplatný identifikátor URI pro přesměrování, autorizační server informuje klienta přidáním následujících parametrů do fragmentové komponenty identifikátoru URI pro přesměrování pomocí formátu "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p \>     : klientská aplikace musí odeslat uživatele do koncového bodu autorizace, aby mohl zahájit proces OAuth.      Na koncovém bodu autorizace se uživatel ověří a pak udělí nebo odepře přístup k aplikaci. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     Pokud vlastník prostředku udělí žádost o přístup, autorizační server vydá přístupový token a doručí ho klientovi přidáním následujících parametrů k součásti fragmentu identifikátoru URI pro přesměrování pomocí formátu "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Tok autorizačního kódu je optimalizovaný pro klienty, kteří můžou udržovat utajení svých přihlašovacích údajů (třeba aplikace webového serveru implementované pomocí PHP, Java, Pythonu, Ruby, ASP.NET atd.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Udělení autorizačního kódu|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Tok přihlašovacích údajů klienta je vhodný v případech, kdy klient (vaše aplikace) požaduje přístup k chráněným prostředkům v rámci svého řízení. Klient se považuje za vlastníka prostředku, takže se nevyžaduje žádná interakce koncového uživatele.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Udělení přihlašovacích údajů klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicitní tok je optimalizován pro klienty, kteří nemůžou zachovávat důvěrnost svých přihlašovacích údajů známých k provozování konkrétního identifikátoru URI pro přesměrování. Tito klienti jsou obvykle implementováni v prohlížeči pomocí skriptovacího jazyka, jako je například JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicitní udělení|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Tok přihlašovacích údajů pro heslo vlastníka prostředku je vhodný v případech, kdy vlastník prostředku má vztah důvěryhodnosti s klientem (vaší aplikací), jako je například operační systém zařízení nebo vysoce privilegovaná aplikace. Tento tok je vhodný pro klienty schopné získat přihlašovací údaje vlastníka prostředku (uživatelské jméno a heslo, obvykle pomocí interaktivního formuláře).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Udělení přihlašovacích údajů pro heslo vlastníka prostředku|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p \>   vlastník prostředku poskytne klientovi své uživatelské jméno a heslo. </p \> <p \> klient požaduje přístupový token z koncového bodu tokenu autorizačního serveru tím, že zahrne přihlašovací údaje přijaté od vlastníka prostředku.  Při vytváření žádosti se klient ověřuje pomocí autorizačního serveru. </p \> <p \>     autorizační Server ověří klienta a ověří přihlašovací údaje vlastníka prostředku a v případě platnosti vydá přístupový token. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>  , pokud se požadavek nezdařil při ověřování klienta nebo je neplatný, autorizační Server odpoví stavovým kódem HTTP 400 (špatný požadavek) (Pokud není uvedeno jinak) a obsahuje následující parametry s odpovědí. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>    klient vytvoří požadavek na koncový bod tokenu přidáním následujících parametrů pomocí formátu "application/x-www-form-urlencoded" s kódováním znaků UTF-8 v těle entity požadavku HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>   Pokud je požadavek přístupového tokenu platný a autorizovaný, autorizační server vydá přístupový token a volitelný obnovovací token a vytvoří odpověď přidáním následujících parametrů do těla zprávy odpovědi HTTP pomocí 200 (ok). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Žádost o přístupový token|  
|OAuth2Step_AuthorizationRequest_Name|Žádost o autorizaci|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Požadovanou. Přístupový token vydaný autorizačním serverem|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Požadovanou. Přístupový token vydaný autorizačním serverem|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Požadovanou. Přístupový token vydaný autorizačním serverem|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Požadovanou. Přístupový token vydaný autorizačním serverem|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Požadovanou. Identifikátor klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|VYŽADUJE se, pokud se klient neověřuje pomocí autorizačního serveru.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Požadovanou. Identifikátor klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Požadovanou. Autorizační kód vygenerovaný autorizačním serverem.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Požadovanou. Autorizační kód přijatý od autorizačního serveru.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Volitelné. Uživatelsky čitelný text ASCII, který poskytuje další informace|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Volitelné. Uživatelsky čitelný text ASCII, který poskytuje další informace|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Volitelné. Uživatelsky čitelný text ASCII, který poskytuje další informace|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Volitelné. Uživatelsky čitelný text ASCII, který poskytuje další informace|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Volitelné. Uživatelsky čitelný text ASCII, který poskytuje další informace|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelný na člověka s informacemi o chybě.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelný na člověka s informacemi o chybě.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelný na člověka s informacemi o chybě.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelný na člověka s informacemi o chybě.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Volitelné. Identifikátor URI identifikující webovou stránku čitelný na člověka s informacemi o chybě.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Požadovanou. Jeden kód chyby ASCII z následujících: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Požadovanou. Jedna z následujících kódů chyby ASCII: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Požadovanou. Jedna z následujících kódů chyby ASCII: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Požadovanou. Jeden kód chyby ASCII z následujících: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Požadovanou. Jedna z následujících kódů chyby ASCII: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Doporučil. Doba života přístupového tokenu v sekundách.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Doporučil. Doba života přístupového tokenu v sekundách.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Doporučil. Doba života přístupového tokenu v sekundách.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Doporučil. Doba života přístupového tokenu v sekundách.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Požadovanou. Hodnota musí být nastavena na hodnotu "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Požadovanou. Hodnota musí být nastavena na hodnotu "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Požadovanou. Hodnota musí být nastavená na Password (heslo).|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Požadovanou. Heslo vlastníka prostředku.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Volitelné. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|POVINNÉ, pokud byl parametr "redirect_uri" zahrnut v žádosti o autorizaci a jejich hodnoty musí být identické.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Volitelné. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Volitelné. Obnovovací token, který lze použít k získání nových přístupových tokenů.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Volitelné. Obnovovací token, který lze použít k získání nových přístupových tokenů.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Volitelné. Obnovovací token, který lze použít k získání nových přístupových tokenů.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Požadovanou. Hodnota musí být nastavená na "Code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Požadovanou. Hodnota musí být nastavena na hodnotu token.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Volitelné. Rozsah žádosti o přístup|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|NEPOVINNÉ, pokud se shoduje s rozsahem požadovaným klientem; v opačném případě se vyžaduje.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Volitelné. Rozsah žádosti o přístup|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|VOLITELNÉ, pokud se shoduje s rozsahem požadovaným klientem; v opačném případě se vyžaduje.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Volitelné. Rozsah žádosti o přístup|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|NEPOVINNÉ, pokud se shoduje s rozsahem požadovaným klientem; v opačném případě se vyžaduje.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Volitelné. Rozsah žádosti o přístup|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VOLITELNÉ, pokud se shoduje s rozsahem požadovaným klientem; v opačném případě se vyžaduje.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|VYŽADUJE se v případě, že se v žádosti o autorizaci klienta vyskytl parametr State.  Přesná hodnota přijatá z klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Doporučil. Neprůhledná hodnota, kterou klient používá k udržení stavu mezi požadavkem a zpětným voláním.  Autorizační Server tuto hodnotu zahrnuje při přesměrování agenta uživatele zpět na klienta.  Parametr by měl být použit pro prevenci padělání požadavků mezi weby.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|VYŽADUJE se v případě, že se v žádosti o autorizaci klienta vyskytl parametr State.  Přesná hodnota přijatá z klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|VYŽADUJE se v případě, že se v žádosti o autorizaci klienta vyskytl parametr State.  Přesná hodnota přijatá z klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Doporučil. Neprůhledná hodnota, kterou klient používá k udržení stavu mezi požadavkem a zpětným voláním.  Autorizační Server tuto hodnotu zahrnuje při přesměrování agenta uživatele zpět na klienta.  Parametr by měl být použit pro prevenci padělání požadavků mezi weby.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|VYŽADUJE se v případě, že se v žádosti o autorizaci klienta vyskytl parametr State.  Přesná hodnota přijatá z klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Požadovanou. Typ vydaného tokenu.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Požadovanou. Typ vydaného tokenu.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Požadovanou. Typ vydaného tokenu.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Požadovanou. Typ vydaného tokenu.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Požadovanou. Uživatelské jméno vlastníka prostředku.|  
|OAuth2UnsupportedTokenType|Typ tokenu {0} není podporován.|  
|OAuth2InvalidState|Neplatná odpověď od autorizačního serveru|  
|OAuth2GrantType_AuthorizationCode|Autorizační kód|  
|OAuth2GrantType_Implicit|Implicitní|  
|OAuth2GrantType_ClientCredentials|Přihlašovací údaje klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Heslo vlastníka prostředku|  
|WebDocumentation302Code|302 Nalezeno|  
|WebDocumentation400Code|400 (chybný požadavek)|  
|OAuth2SendingMethod_AuthHeader|Autorizační hlavička|  
|OAuth2SendingMethod_QueryParam|Parametr dotazu|  
|OAuth2AuthorizationServerGeneralException|Došlo k chybě při autorizaci přístupu přes {0}|  
|OAuth2AuthorizationServerCommunicationException|Nebylo možné navázat připojení HTTP k autorizačnímu serveru nebo bylo neočekávaně ukončeno.|  
|WebDocumentationOAuth2GeneralErrorMessage|Došlo k neočekávané chybě.|  
|AuthorizationServerCommunicationException|Došlo k výjimce komunikace autorizačního serveru. Kontaktujte prosím správce.|  
|TextblockSubscriptionKeyHeaderDescription|Klíč předplatného, který poskytuje přístup k tomuto rozhraní API. Nachází se ve vašem <profilu href = '/Developer ' \></a \> .|  
|TextblockOAuthHeaderDescription|Přístupový token OAuth 2,0 získaný z <i \> {0}</i \> . Podporované typy udělení: <i \> {1}</i \> .|  
|TextblockContentTypeHeaderDescription|Typ média těla odesílaného do rozhraní API|  
|ErrorMessageApiNotAccessible|Rozhraní API, které se pokoušíte volat, není v tuto chvíli k dispozici. Kontaktujte prosím vydavatele rozhraní API <sem href = "/issues" \></a \> .|  
|ErrorMessageApiTimedout|Rozhraní API, které se pokoušíte volat, trvá déle než obvykle, aby bylo možné získat odpověď zpět. Kontaktujte prosím vydavatele rozhraní API <sem href = "/issues" \></a \> .|  
|BadRequestParameterExpected|{0}očekává se parametr "".|  
|TooltipTextDoubleClickToSelectAll|Dvojím kliknutím můžete vybrat vše.|  
|TooltipTextHideRevealSecret|Zobrazit/skrýt|  
|ButtonLinkOpenConsole|Vyzkoušet|  
|SectionHeadingRequestBody|Text požadavku|  
|SectionHeadingRequestParameters|Parametry žádosti|  
|SectionHeadingRequestUrl|Adresa URL požadavku|  
|SectionHeadingResponse|Odpověď|  
|SectionHeadingRequestHeaders|Hlavičky požadavku|  
|FormLabelSubtextOptional|optional|  
|SectionHeadingCodeSamples|Ukázky kódů|  
|TextblockOpenidConnectHeaderDescription|Token ID OpenID Connect získaný z <i \> {0}</i \> Podporované typy udělení: <i \> {1}</i \> .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelBack|návrat|  
|LinkLabelHomePage|Domovská stránka|  
|LinkLabelSendUsEmail|Pošlete nám e-mail|  
|PageTitleError|Je nám líto, ale při obsluze požadované stránky došlo k problému.|  
|TextblockPotentialCauseIntermittentIssue|Může se jednat o přerušovaný problém s přístupem k datům, který už je pryč.|  
|TextblockPotentialCauseOldLink|Odkaz, na který jste klikli, může být Starý a už neodkazuje na správné umístění.|  
|TextblockPotentialCauseTechnicalProblem|Na našem konci může být technický problém.|  
|TextblockPotentialSolutionRefresh|Zkuste stránku aktualizovat.|  
|TextblockPotentialSolutionStartOver|Začněte znovu z našeho webu {0} .|  
|TextblockPotentialSolutionTryAgain|Opakujte akci, {0} kterou jste provedli znovu.|  
|TextReportProblem|{0} Popisuje, co se nepovedlo, a my se na něj podíváme hned, jak můžeme.|  
|TitlePotentialCause|Potenciální Příčina|  
|TitlePotentialSolution|Je možné, že je to jen dočasný problém, pár věcí k vyzkoušení|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a> IssuesStrings  
  
|Name|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problémy|  
|WebIssuesNoActiveSubscriptions|Nemáte žádná aktivní předplatná. Abyste mohli ohlásit problém, musíte se přihlásit k odběru produktu.|  
|WebIssuesNotSignin|Nejste přihlášení. {0}Oznamte problém nebo odešlete komentář.|  
|WebIssuesReportIssueButton|Nahlásit problém|  
|WebIssuesSignIn|přihlášení|  
|WebIssuesStatusReportedBy|Stav: {0} &#124; ohlásil (a) {1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name|Text|  
|----------|----------|  
|LinkLabelHomePage|Domovská stránka|  
|LinkLabelSendUsEmail|Pošlete nám e-mail|  
|PageTitleNotFound|Omlouváme se, ale nenašli jsme stránku, kterou hledáte.|  
|TextblockPotentialCauseMisspelledUrl|Pokud jste zadali adresu URL v, možná jste chybu nanapsali.|  
|TextblockPotentialCauseOldLink|Odkaz, na který jste klikli, může být Starý a už neodkazuje na správné umístění.|  
|TextblockPotentialSolutionRetype|Zkuste znovu zadat adresu URL.|  
|TextblockPotentialSolutionStartOver|Začněte znovu z našeho webu {0} .|  
|TextReportProblem|{0} Popisuje, co se nepovedlo, a my se na něj podíváme hned, jak můžeme.|  
|TitlePotentialCause|Potenciální Příčina|  
|TitlePotentialSolution|Potenciální řešení|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name|Text|  
|----------|----------|  
|WebProductsAgreement|Po přihlášení k odběru produktu Souhlasím s {0} `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>` .|  
|WebProductsLegalTermsLink|Podmínky použití|  
|WebProductsSubscribeButton|Přihlásit k odběru|  
|WebProductsUsageLimitsHeader|Limity využití|  
|WebProductsYouAreNotSubscribed|Přihlásíte se k odběru tohoto produktu.|  
|WebProductsYouRequestedSubscription|Požádali jste o předplatné tohoto produktu.|  
|ErrorYouNeedToAgreeWithLegalTerms|Než budete moct pokračovat, musíte souhlasit s podmínkami použití.|  
|ButtonLabelAddSubscription|Přidat předplatné|  
|LinkLabelChangeSubscriptionName|mění|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|{0}K tomuto produktu máte předplatné:|  
|TextblockSingleSubscriptionsCount|{0}K tomuto produktu máte předplatné:|  
|TextblockSingleApisCount|Tento produkt obsahuje {0} rozhraní API:|  
|TextblockMultipleApisCount|Tento produkt obsahuje {0} rozhraní API:|  
|TextblockHeaderSubscribe|Přihlášení k odběru produktu|  
|TextblockSubscriptionDescription|Nové předplatné se vytvoří takto:|  
|TextblockSubscriptionLimitReached|Byl dosažen limit předplatných.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a> ProductsStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Správce v tuto chvíli zakáže přihlášení.|  
|TextboxExternalIdentitiesSigninInvitation|Případně se přihlaste pomocí|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Přihlaste se pomocí:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a> SigninResources  
  
|Name|Text|  
|----------|----------|  
|PrincipalNotFound|Objekt zabezpečení nebyl nalezen nebo je neplatný podpis|  
|ErrorSsoAuthenticationFailed|Ověření jednotného přihlašování se nezdařilo|  
|ErrorSsoAuthenticationFailedDetailed|Byl zadán neplatný token nebo nelze ověřit podpis.|  
|ErrorSsoTokenInvalid|Token jednotného přihlašování je neplatný.|  
|ValidationErrorSpecificEmailAlreadyExists|E-mail {0} už je zaregistrovaný.|  
|ValidationErrorSpecificEmailInvalid|E-mail {0} je neplatný.|  
|ValidationErrorPasswordInvalid|Heslo je neplatné. Opravte prosím chyby a zkuste to znovu.|  
|PropertyTooShort|{0} je příliš krátké|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Neplatná e-mailová adresa|  
|ValidationMessageNewPasswordConfirmationRequired|Potvrďte nové heslo|  
|ValidationErrorPasswordConfirmationRequired|Potvrzení hesla je prázdné.|  
|WebAuthenticationEmailChangeNotice|E-mail pro potvrzení změn je na cestě {0} . Podle pokynů v rámci této služby potvrďte novou e-mailovou adresu. Pokud e-mail nepřijde do vaší doručené pošty během několika dalších minut, zkontrolujte složku nevyžádaných e-mailů.|  
|WebAuthenticationEmailChangeNoticeHeader|Vaše žádost o změnu e-mailu byla úspěšně zpracována.|  
|WebAuthenticationEmailChangeNoticeTitle|Požadována změna e-mailu|  
|WebAuthenticationEmailHasBeenRevertedNotice|E-mail již existuje. Požadavek byl vrácen zpět.|  
|ValidationErrorEmailAlreadyExists|E-mail už existuje.|  
|ValidationErrorEmailInvalid|Neplatná e-mailová adresa|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|E-mail je povinný.|  
|WebAuthenticationErrorNoticeHeader|Chyba|  
|WebAuthenticationFieldLengthErrorMessage|{0} musí mít maximální délku {1}|  
|TextboxLabelEmailFirstName|Jméno|  
|ValidationErrorFirstNameRequired|Jméno je povinné.|  
|ValidationErrorFirstNameInvalid|Neplatné křestní jméno|  
|NoticeInvalidInvitationToken|Upozorňujeme, že potvrzovací odkazy platí jenom pro 48 hodin. Pokud jste v tomto období stále, ujistěte se, že je váš odkaz správný. Pokud váš odkaz vypršel, opakujte akci, kterou se pokoušíte potvrdit.|  
|NoticeHeaderInvalidInvitationToken|Neplatný token pozvánky|  
|NoticeTitleInvalidInvitationToken|Chyba potvrzení|  
|WebAuthenticationLastNameInvalidErrorMessage|Neplatné příjmení|  
|TextboxLabelEmailLastName|Příjmení|  
|ValidationErrorLastNameRequired|Příjmení je povinné.|  
|WebAuthenticationLinkExpiredNotice|Vypršela potvrzovací odkaz, který vám poslal. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Odkaz na resetování hesla je neplatný nebo vypršel jeho platnost.|  
|WebAuthenticationLinkExpiredNoticeTitle|Odkaz odeslán|  
|WebAuthenticationNewPasswordLabel|Nové heslo|  
|ValidationMessageNewPasswordRequired|Je vyžadováno nové heslo.|  
|TextboxLabelNotificationsSenderEmail|E-mail odesílatele oznámení|  
|TextboxLabelOrganizationName|Název organizace|  
|WebAuthenticationOrganizationRequiredErrorMessage|Název organizace je prázdný.|  
|WebAuthenticationPasswordChangedNotice|Vaše heslo se úspěšně aktualizovalo.|  
|WebAuthenticationPasswordChangedNoticeTitle|Heslo Aktualizováno|  
|WebAuthenticationPasswordCompareErrorMessage|Hesla se neshodují.|  
|WebAuthenticationPasswordConfirmLabel|Potvrzení hesla|  
|ValidationErrorPasswordInvalidDetailed|Heslo je příliš slabé.|  
|WebAuthenticationPasswordLabel|Heslo|  
|ValidationErrorPasswordRequired|Heslo je povinné.|  
|WebAuthenticationPasswordResetSendNotice|Změna e-mailu s potvrzením hesla je na cestě {0} . Postupujte prosím podle pokynů v e-mailu a pokračujte v procesu změny hesla.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Vaše žádost o resetování hesla byla úspěšně zpracována.|  
|WebAuthenticationPasswordResetSendNoticeTitle|Požadováno resetování hesla|  
|WebAuthenticationRequestNotFoundNotice|Žádost nebyla nalezena.|  
|WebAuthenticationSenderEmailRequiredErrorMessage|E-mail odesílatele oznámení je prázdný.|  
|WebAuthenticationSigninPasswordLabel|Potvrďte prosím změnu zadáním hesla.|  
|WebAuthenticationSignupConfirmNotice|Potvrzovací e-mail registrace je na cestě {0} . <br/ \> postupujte podle pokynů v e-mailu a aktivujte svůj účet. <br/ \> Pokud e-mail nepřijde do vaší doručené pošty během několika dalších minut, zkontrolujte složku nevyžádaných e-mailů.|  
|WebAuthenticationSignupConfirmNoticeHeader|Váš účet byl úspěšně vytvořen.|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|E-mail s potvrzením registrace se odeslal znovu.|  
|WebAuthenticationSignupConfirmNoticeTitle|Účet vytvořen|  
|WebAuthenticationTokenRequiredErrorMessage|Token je prázdný.|  
|WebAuthenticationUserAlreadyRegisteredNotice|Zdá se, že uživatel s tímto e-mailem je už v systému zaregistrovaný. Pokud jste zapomněli heslo, zkuste ho prosím obnovit nebo se obraťte na náš tým podpory.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Uživatel je už zaregistrovaný.|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Již registrováno|  
|ButtonLabelChangePassword|Změna hesla|  
|ButtonLabelChangeAccountInfo|Změnit informace o účtu|  
|ButtonLabelCloseAccount|Zavřít účet|  
|WebAuthenticationInvalidCaptchaErrorMessage|Zadaný text se neshoduje s textem na obrázku. Zkuste to prosím znovu.|  
|ValidationErrorCredentialsInvalid|E-mail nebo heslo nejsou platné. Opravte prosím chyby a zkuste to znovu.|  
|WebAuthenticationRequestIsNotValid|Žádost není platná.|  
|WebAuthenticationUserIsNotConfirm|Před pokusem o přihlášení prosím potvrďte svou registraci.|  
|WebAuthenticationInvalidEmailFormated|E-mail je neplatný: {0}|  
|WebAuthenticationUserNotFound|Uživatel nenalezen|  
|WebAuthenticationTenantNotRegistered|Váš účet patří do tenanta Azure Active Directory, který nemá autorizaci pro přístup k tomuto portálu.|  
|WebAuthenticationAuthenticationFailed|Ověřování se nezdařilo.|  
|WebAuthenticationGooglePlusNotEnabled|Ověřování se nezdařilo. Pokud jste aplikaci povolili, obraťte se prosím na správce a ujistěte se, že je správně nakonfigurované ověřování Google.|  
|ValidationErrorAllowedTenantIsRequired|Povolený tenant je povinný.|  
|ValidationErrorTenantIsNotValid|Tenant Azure Active Directory ' {0} ' není platný.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Přihlaste se pomocí svého {0} účtu.|  
|WebAuthenticationUserLimitNotice|Tato služba dosáhla maximálního počtu povolených uživatelů. `<a href="mailto:{0}"\>contact the administrator</a\>`Upgradujte prosím službu a znovu povolte registraci uživatele.|  
|WebAuthenticationUserLimitNoticeHeader|Registrace uživatele zakázána|  
|WebAuthenticationUserLimitNoticeTitle|Registrace uživatele zakázána|  
|WebAuthenticationUserRegistrationDisabledNotice|Správce zakázal registraci uživatelů. Přihlaste se prosím pomocí externího zprostředkovatele identity.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registrace uživatele zakázána|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registrace uživatele zakázána|  
|WebAuthenticationSignupPendingConfirmationNotice|Aby bylo možné dokončit vytváření vašeho účtu, potřebujeme ověřit vaši e-mailovou adresu. Poslali jsme vám e-mail na adresu {0} . Pokud chcete aktivovat svůj účet, postupujte podle pokynů v e-mailu. Pokud e-mail nepřijde do několika dalších minut, zkontrolujte složku nevyžádaných e-mailů.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Našli jsme nepotvrzený účet pro e-mailovou adresu {0} . Abychom mohli dokončit vytváření vašeho účtu, musíme ověřit vaši e-mailovou adresu. Poslali jsme vám e-mail na adresu {0} . Pokud chcete aktivovat svůj účet, postupujte podle pokynů v e-mailu. Pokud e-mail nepřijde do několika dalších minut, zkontrolujte složku nevyžádaných e-mailů.|  
|WebAuthenticationSignupConfirmationAlmostDone|Skoro hotovo|  
|WebAuthenticationSignupConfirmationEmailSent|Poslali jsme vám e-mail na adresu {0} . Pokud chcete aktivovat svůj účet, postupujte podle pokynů v e-mailu. Pokud e-mail nepřijde do několika dalších minut, zkontrolujte složku nevyžádaných e-mailů.|  
|WebAuthenticationEmailSentNotificationMessage|E-mail se úspěšně odeslal. {0}|  
|WebAuthenticationNoAadTenantConfigured|Pro tuto službu není nakonfigurovaný žádný tenant Azure Active Directory.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Souhlasím s `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>` .|  
|TextblockUserRegistrationTermsProvided|Přečtěte si prosím `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Podmínky použití|  
|ValidationMessageConsentNotAccepted|Než budete moct pokračovat, musíte souhlasit s podmínkami použití.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a> SigninStrings  
  
|Name|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Forgot your password?|  
|WebAuthenticationIfAdministrator|Pokud jste správce, musíte se přihlásit `<a href="{0}"\>here</a\>` .|  
|WebAuthenticationNotAMember|Not a member yet? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Zapamatovat si mě na tomto počítači|  
|WebAuthenticationSigininWithPassword|Přihlaste se pomocí uživatelského jména a hesla.|  
|WebAuthenticationSigninTitle|Přihlásit se|  
|WebAuthenticationSignUpNow|Zaregistrujte se|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a> SignupStrings  
  
|Name|Text|  
|----------|----------|  
|PageTitleSignup|Registrace|  
|WebAuthenticationAlreadyAMember|Již jste členem?|  
|WebAuthenticationCreateNewAccount|Vytvořit nový účet API Management|  
|WebAuthenticationSigninNow|Přihlaste se hned|  
|ButtonLabelSignup|Registrace|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Opravdu chcete zrušit toto předplatné?|  
|SubscriptionRenewConfirmation|Opravdu chcete obnovit toto předplatné?|  
|WebDevelopersManageSubscriptions|Správa předplatných|  
|WebDevelopersPrimaryKey|Primární klíč|  
|WebDevelopersRegenerateLink|Znovu vytvořit|  
|WebDevelopersSecondaryKey|Sekundární klíč|  
|ButtonLabelShowKey|Zobrazit|  
|ButtonLabelRenewSubscription|Obnovit|  
|WebDevelopersSubscriptionRequested|Požadováno dne {0}|  
|WebDevelopersSubscriptionRequestedState|Požádáno|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|Stav|  
|WebDevelopersUsageStatisticsLink|Analytické sestavy|  
|WebDevelopersYourSubscriptions|Vaše předplatná|  
|SubscriptionPropertyLabelRequestedDate|Požadováno dne|  
|SubscriptionPropertyLabelStartedDate|Zahájeno|  
|PageTitleRenameSubscription|Přejmenovat předplatné|  
|SubscriptionPropertyLabelName|Název předplatného|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Chcete zavřít svůj účet?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Skrýt|  
|ButtonLabelRegenerateKey|Znovu vytvořit|  
|InformationMessageKeyWasRegenerated|Opravdu chcete znovu vygenerovat tento klíč?|  
|ButtonLabelShowKey|Zobrazit|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizovat profil|  
|PageTitleUpdateProfile|Aktualizovat informace o účtu|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a> Profilu  
  
|Name|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Změnit informace o účtu|  
|ButtonLabelChangePassword|Změna hesla|  
|ButtonLabelCloseAccount|Zavřít účet|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Jméno|  
|TextboxLabelEmailLastName|Příjmení|  
|TextboxLabelNotificationsSenderEmail|E-mail odesílatele oznámení|  
|TextboxLabelOrganizationName|Název organizace|  
|SubscriptionStateActive|Aktivní|  
|SubscriptionStateCancelled|Cancelled|  
|SubscriptionStateExpired|Platnost vypršela|  
|SubscriptionStateRejected|Zamítnuto|  
|SubscriptionStateRequested|Požádáno|  
|SubscriptionStateSuspended|Dočasně blokován.|  
|DefaultSubscriptionNameTemplate|{0}  výchozí|  
|SubscriptionNameTemplate|Přístup pro vývojáře #{0}|  
|TextboxLabelSubscriptionName|Název předplatného|  
|ValidationMessageSubscriptionNameRequired|Název předplatného nemůže být prázdný.|  
|ApiManagementUserLimitReached|Tato služba dosáhla maximálního počtu povolených uživatelů. Upgradujte prosím na vyšší cenovou úroveň.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a> Prostředky glyfů  
 Šablony portálu pro vývojáře API Management můžou použít glyfy ze [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons). Tato sada glyfů zahrnuje více než 250 glyfů ve formátu písma ze halflings sady [Glyphicon](https://glyphicons.com/) . Chcete-li použít glyf z této sady, použijte následující syntaxi.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Úplný seznam glyfů naleznete v tématu [Glyphicons from Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
