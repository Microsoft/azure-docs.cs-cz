---
title: Šablony prostředků Azure API Management | Dokumentace Microsoftu
description: Další informace o studijních materiálech, které jsou k dispozici pro použití v šablon portálu pro vývojáře ve službě Azure API Management.
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
ms.openlocfilehash: 4a7e3e9ff414c8f7ba1c49924f1d8659d72ae3e2
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158206"
---
# <a name="azure-api-management-template-resources"></a>Šablony prostředků Azure API Management
Azure API Management nabízí následující typy prostředků pro použití v vývojář šablon portálu.  
  
-   [Řetězcové prostředky](#strings)  
  
-   [Piktogram prostředky](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> Řetězcové prostředky  
 API Management nabízí komplexní sadu řetězcových prostředků pro použití na portálu pro vývojáře. Tyto prostředky jsou lokalizovány do všech jazyků podporovaných službou API Management. Výchozí sada šablony používá tyto prostředky pro záhlaví stránky, popisků a jakékoli konstantní řetězce, které se zobrazují na portálu pro vývojáře. Použít prostředek řetězce v šablonách, poskytují předpona řetězce prostředku, za nímž následuje název řetězce, jak je znázorněno v následujícím příkladu.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Následující příklad je z šablony seznamu produktů a zobrazí **produkty** v horní části stránky.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Najdete v následujících tabulkách pro řetězcové prostředky k dispozici pro použití ve vašich šablon portálu pro vývojáře. Název tabulky použijte jako předpona pro řetězcové prostředky v této tabulce.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentace ke službě](#Documentation)  
  
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
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Název|Text|  
|----------|----------|  
|PageTitleApis|Rozhraní API|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Název|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Náhled aplikace|  
|WebApplicationsRequirementsHeader|Požadavky|  
|WebApplicationsScreenshotAlt|Snímek obrazovky|  
|WebApplicationsScreenshotsHeader|Snímky obrazovek|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Název|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Opravdu chcete odebrat aplikaci?|  
|WebDevelopersAppNotPublished|Nepublikováno|  
|WebDevelopersAppNotSubmitted|Neodeslané|  
|WebDevelopersAppTableCategoryHeader|Kategorie|  
|WebDevelopersAppTableNameHeader|Název|  
|WebDevelopersAppTableStateHeader|Stav|  
|WebDevelopersEditLink|Upravit|  
|WebDevelopersRegisterAppLink|Zaregistrovat aplikaci|  
|WebDevelopersRemoveLink|Odebrat|  
|WebDevelopersSubmitLink|Odeslat|  
|WebDevelopersYourApplicationsHeader|Vaše aplikace|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Název|Text|  
|----------|----------|  
|WebApplicationsHeader|Aplikace|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Název|Text|  
|----------|----------|  
|NoItemsToDisplay|Nenašli jsme žádné výsledky.|  
|GeneralExceptionMessage|Něco není správný. Může se jednat o dočasnou poruchu nebo chybu. Zkuste to znovu.|  
|GeneralJsonExceptionMessage|Něco není správný. Může se jednat o dočasnou poruchu nebo chybu. Prosím znovu načíst stránku a zkuste to znovu.|  
|ConfirmationMessageUnsavedChanges|Existují nějaké neuložené změny. Opravdu že chcete zrušit a zahodit změny?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Text požadavku HTTP je moc dlouhý.|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Název|Text|  
|----------|----------|  
|ButtonLabelCancel|Zrušit|  
|ButtonLabelSave|Uložení|  
|GeneralExceptionMessage|Něco není správný. Může se jednat o dočasnou poruchu nebo chybu. Zkuste to znovu.|  
|NoItemsToDisplay|Nejsou k dispozici žádné položky, které by bylo možné zobrazit.|  
|PagerButtonLabelFirst|První|  
|PagerButtonLabelLast|Poslední|  
|PagerButtonLabelNext|Další|  
|PagerButtonLabelPrevious|Předchozí|  
|PagerLabelPageNOfM|Stránka {0} z {1}|  
|PasswordTooShort|Heslo je příliš krátké.|  
|EmailAsPassword|Nepoužívejte svůj e-mail jako heslo.|  
|PasswordSameAsUserName|Vaše heslo nesmí obsahovat vaše uživatelské jméno.|  
|PasswordTwoCharacterClasses|Použijte jiné třídy znaků.|  
|PasswordTooManyRepetitions|Příliš mnoho opakování|  
|PasswordSequenceFound|Vaše heslo obsahuje sekvence.|  
|PagerLabelPageSize|Velikost stránky|  
|CurtainLabelLoading|Načítání...|  
|TablePlaceholderNothingToDisplay|Pro vybrané období a obor nejsou k dispozici žádná data.|  
|ButtonLabelClose|Zavřít|  
  
###  <a name="Documentation"></a> Dokumentace ke službě  
  
|Název|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Neplatná hlavička "{0}.|  
|WebDocumentationInvalidRequestErrorMessage|Neplatná adresa URL žádosti|  
|TextboxLabelAccessToken|Access token *|  
|DropdownOptionPrimaryKeyFormat|Primární –{0}|  
|DropdownOptionSecondaryKeyFormat|Sekundární-{0}|  
|WebDocumentationSubscriptionKeyText|Váš klíč předplatného|  
|WebDocumentationTemplatesAddHeaders|Přidat povinné hlavičky HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Ukázka Basic Authorization|  
|WebDocumentationTemplatesCurlForBasicAuth|pro Basic Authorization použijte: --user {uživatelské jméno}:{heslo}|  
|WebDocumentationTemplatesCurlValuesForPath|Zadejte hodnoty parametrů cesty (zobrazené jako {...}), klíč předplatného a hodnoty parametrů dotazu.|  
|WebDocumentationTemplatesDeveloperKey|Zadejte klíč předplatného.|  
|WebDocumentationTemplatesJavaApache|Tato ukázka používá klient Apache HTTP ze součástí HTTP)http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Podle potřeby zadejte hodnoty nepovinných parametrů.|  
|WebDocumentationTemplatesPhpPackage|Tato ukázka používá balíček HTTP_Request2. (Další informace: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Podle potřeby zadejte hodnoty parametrů cesty (zobrazené jako {...}) a text žádosti.|  
|WebDocumentationTemplatesRequestBody|Zadejte text žádosti.|  
|WebDocumentationTemplatesRequiredParams|Zadejte hodnoty následujících povinných parametrů.|  
|WebDocumentationTemplatesValuesForPath|Zadejte hodnoty parametrů cesty (zobrazené jako {...}).|  
|OAuth2AuthorizationEndpointDescription|Koncový bod autorizace se používá k interakci s vlastníkem prostředku a získání udělení autorizace.|  
|OAuth2AuthorizationEndpointName|Koncový bod autorizace|  
|OAuth2TokenEndpointDescription|Koncový bod tokenu používá klient ke získání přístupového tokenu tím, že předloží svoje udělení autorizace nebo aktualizační token.|  
|OAuth2TokenEndpointName|Koncový bod tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> klienta spouští tok přesměrováním vlastník prostředku uživatelského agenta pro koncový bod autorizace.  Klient zahrne identifikátoru klienta, požadovaný obor, místní a identifikátor URI přesměrování, na které pošle autorizační server uživatelského agenta, zpět po přístup je udělen (nebo byl odepřen).     < /p\> < p\> autorizační server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a zjistí, zda vlastník prostředku udělí nebo zamítne žádost o přístup klienta.     < /p\> < p\> za předpokladu, že vlastník prostředku udělí přístup, přesměruje autorizační server uživatelského agenta zpět na klienta pomocí přesměrovacího identifikátoru URI zadaný starší (v požadavku nebo během registrati klienta zapnuto).  Přesměrovací identifikátor URI obsahuje autorizační kód a místní stav, který klient poskytl dříve.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Pokud uživatel odmítne žádost o přístup z, pokud požadavek je neplatný, bude klient informován pomocí následujících parametrů přidaných do přesměrování: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Žádost o autorizaci|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientská aplikace musí kvůli zahájení procesu OAuth poslat uživateli pro koncový bod autorizace.          Koncovém bodu autorizace uživatel ověří a pak udělí nebo zamítne přístup k aplikaci.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> za předpokladu, že vlastník prostředku udělí přístup, přesměruje autorizační server uživatelského agenta zpět na klienta pomocí přesměrovacího identifikátoru URI zadaný starší (v žádosti nebo při registraci klienta).  Přesměrovací identifikátor URI obsahuje autorizační kód a místní stav, který klient poskytl dříve. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> si klient vyžádá přístupový token od autorizačního serveru '' koncový bod tokenu s zahrnutím autorizační kód přijatý v předchozím kroku.  Když se odešle požadavek, klient se ověří pomocí autorizačního serveru.  Klient obsahuje přesměrovací identifikátor URI používá k získání autorizačního kódu pro ověřování. < /p\> < p\> autorizační server ověří klienta, ověří autorizační kód a zajišťuje, že přesměrovací identifikátor URI přijatých odpovídá identifikátoru URI pro přesměrování klienta v kroku (C).  Pokud je platný, odpoví autorizační server zpět pomocí přístupového tokenu a volitelně obnovovací token. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> Pokud žádost o ověření klienta se nezdařilo nebo je neplatný, autorizační server odpoví stavovým kódem HTTP 400 (chybná žádost) (Pokud není určeno jinak) a zahrne do odpovědi následující parametry. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> klient učiní žádost vůči koncovému bodu tokenu odesláním následujících parametrů pomocí "application/x--www-form-urlencoded" formátu s kódováním znaků UTF-8 v HTTP požadavku obsah entity. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> autorizační server vystaví přístupový token a volitelný aktualizační token a sestaví odpověď přidáním následujících parametrů do entity-body odpovědi HTTP s kódem stavový kód 200 (OK). < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> klient ověřuje pomocí autorizačního serveru a vyžádá přístupový token z koncového bodu tokenu. < /p\> < p\> autorizační server ověří klienta a pokud je platný, vystaví přístupový token. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Pokud žádost neprošla ověřením klienta nebo je neplatný autorizační server stavovým kódem HTTP 400 (chybná žádost) odpovídá (Pokud není určeno jinak) a zahrne do odpovědi následující parametry. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient učiní žádost vůči koncovému bodu tokenu přidáním následujících parametrů pomocí "application/x--www-form-urlencoded" formátu s kódováním znaků UTF-8 v HTTP požadavku obsah entity. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Pokud žádost o přístupový token platná a autorizovaná, autorizační server vystaví přístupový token a volitelný aktualizační token a sestaví odpověď přidáním následujících parametrů do entity-body odpovědi HTTP se 200  Stavový kód (OK). < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> klienta spouští tok přesměrováním vlastníka prostředku "s uživatelského agenta pro koncový bod autorizace.  Klient zahrne identifikátoru klienta, požadovaný obor, místní a identifikátor URI přesměrování, na které pošle autorizační server uživatelského agenta, zpět po přístup je udělen (nebo byl odepřen). < /p\> < p\> autorizační server ověří vlastníka prostředku (prostřednictvím uživatelského agenta) a zjistí, zda vlastník prostředku udělí nebo odepře klienta "žádost o přístup s. < /p\> < p\> za předpokladu, že vlastník prostředku udělí přístup, přesměruje autorizační server uživatelského agenta zpět na klienta pomocí přesměrovacího identifikátoru URI jste zadali dříve.  Identifikátor URI pro přesměrování zahrne přístupový token fragmentu identifikátoru URI. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Pokud vlastník prostředku žádost o přístup zamítne nebo pokud žádost selže z jiných důvodů než je chybějící nebo neplatný přesměrovací identifikátor URI, informuje autorizační server klienta přidáním následujících parametrů do o součást fragmentu f přesměrovací identifikátor URI ve formátu "application/x--www-form-urlencoded". < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> klientská aplikace musí kvůli zahájení procesu OAuth poslat uživateli pro koncový bod autorizace.      Koncovém bodu autorizace uživatel ověří a pak udělí nebo zamítne přístup k aplikaci. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> Pokud vlastník prostředku udělí žádost o přístup, autorizační server vystaví přístupový token a doručí ho klientovi přidáním následujících parametrů do součásti fragmentu přesměrovacího identifikátoru URI pomocí "application/x-www Formát - formuláře – adresy URL". < /p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Tok autorizačního kódu je optimalizovaný pro klienty, kteří jsou schopni zachovat utajení svých přihlašovacích údajů (například aplikace webového serveru implementované pomocí jazyků PHP, Java, Python, Ruby, ASP.NET atd.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Udělení autorizačního kódu|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Tok klientských přihlašovacích údajů je vhodný v případech, kdy klient (aplikace) požaduje přístup k chráněným prostředkům pod svou kontrolou. Klient se považuje za jako vlastníka prostředku, tak nebude vyžadována žádná interakce s koncovým uživatelem.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Udělení klientských přihlašovacích údajů|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicitní tok je optimalizovaný pro klienti nepodporující zachovat utajení svých přihlašovacích údajů pro provoz konkrétní identifikátor URI pro přesměrování. Tito klienti jsou zpravidla implementovaní v prohlížeči pomocí skriptovacího jazyka, jako je JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicitní udělení|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Tok přihlašovacího hesla vlastníka prostředku je vhodný v případech, kdy má vlastník prostředku vztah důvěryhodnosti s klientem (vaší aplikací), například operační systém zařízení nebo aplikace s vysokou úrovní oprávnění. Tento tok je vhodný pro klienty schopni získat přihlašovací údaje (uživatelské jméno a heslo, zpravidla pomocí interaktivního formuláře) vlastníka prostředku.|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Udělení přihlašovacího hesla vlastníka prostředku|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> vlastník prostředku poskytne klientovi své uživatelské jméno a heslo. < /p\> < p\> si klient vyžádá přístupový token od autorizačního serveru '' koncový bod tokenu s zahrnutím přihlašovacích údajů přijatých od vlastníka prostředku.  Když se odešle požadavek, klient se ověří pomocí autorizačního serveru. < /p\> < p\> autorizační server ověří klienta a ověří přihlašovací údaje vlastníka prostředku a pokud je platný, vystaví přístupový token. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Pokud žádost neprošla ověřením klienta nebo je neplatný autorizační server stavovým kódem HTTP 400 (chybná žádost) odpovídá (Pokud není určeno jinak) a zahrne do odpovědi následující parametry. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> klient učiní žádost vůči koncovému bodu tokenu přidáním následujících parametrů pomocí "application/x--www-form-urlencoded" formátu s kódováním znaků UTF-8 v HTTP požadavku obsah entity. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Pokud žádost o přístupový token platná a autorizovaná, autorizační server vystaví přístupový token a volitelný aktualizační token a sestaví odpověď přidáním následujících parametrů do entity-body odpovědi HTTP 20 Kód stavu 0 (OK). < /p\>|  
|OAuth2Step_AccessTokenRequest_Name|Žádost o přístupový token|  
|OAuth2Step_AuthorizationRequest_Name|Žádost o autorizaci|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|POVINNÉ. Přístupový token vystavený autorizačním serverem.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|POVINNÉ. Přístupový token vystavený autorizačním serverem.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|POVINNÉ. Přístupový token vystavený autorizačním serverem.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|POVINNÉ. Přístupový token vystavený autorizačním serverem.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|POVINNÉ. Identifikátor klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|POVINNÉ, pokud se klient neověřuje pomocí autorizačního serveru.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|POVINNÉ. Identifikátor klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|POVINNÉ. Autorizační kód vygenerovaný autorizačním serverem.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|POVINNÉ. Kód autorizace přijatý od autorizačního serveru.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|VOLITELNÉ. Další informace, čitelný text ASCII.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|VOLITELNÉ. Další informace, čitelný text ASCII.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|VOLITELNÉ. Další informace, čitelný text ASCII.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|VOLITELNÉ. Další informace, čitelný text ASCII.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VOLITELNÉ. Další informace, čitelný text ASCII.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|VOLITELNÉ. Identifikátor URI identifikující čitelné webovou stránku s informacemi o chybě.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|VOLITELNÉ. Identifikátor URI identifikující čitelné webovou stránku s informacemi o chybě.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|VOLITELNÉ. Identifikátor URI identifikující čitelné webovou stránku s informacemi o chybě.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|VOLITELNÉ. Identifikátor URI identifikující čitelné webovou stránku s informacemi o chybě.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|VOLITELNÉ. Identifikátor URI identifikující čitelné webovou stránku s informacemi o chybě.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|POVINNÉ. Jeden kód chyby ASCII z následujících: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|POVINNÉ. Jeden kód chyby ASCII z následujících: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|POVINNÉ. Jeden kód chyby ASCII z následujících: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|POVINNÉ. Jeden kód chyby ASCII z následujících: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|POVINNÉ. Jeden kód chyby ASCII z následujících: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|DOPORUČENÉ. Životnost tokenu přístupu v sekundách.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|DOPORUČENÉ. Životnost tokenu přístupu v sekundách.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|DOPORUČENÉ. Životnost tokenu přístupu v sekundách.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|DOPORUČENÉ. Životnost tokenu přístupu v sekundách.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|POVINNÉ. Hodnota musí být nastavená na "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|POVINNÉ. Hodnota musí být nastavená na "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|POVINNÉ. Hodnota musí být nastavená na "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|POVINNÉ. Heslo vlastníka prostředku.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|VOLITELNÉ. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|POVINNÉ, pokud byl v žádosti o autorizaci zahrnut parametr redirect_uri a jejich hodnoty MUSÍ být identické.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|VOLITELNÉ. Identifikátor URI koncového bodu přesměrování musí být absolutní identifikátor URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|VOLITELNÉ. Token obnovení, který můžete použít k získání nových přístupových tokenů.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|VOLITELNÉ. Token obnovení, který můžete použít k získání nových přístupových tokenů.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|VOLITELNÉ. Token obnovení, který můžete použít k získání nových přístupových tokenů.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|POVINNÉ. Hodnota musí být nastavená na "kód".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|POVINNÉ. Hodnota musí být nastavená na "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|VOLITELNÉ. Rozsah žádosti o přístup.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|NEPOVINNÉ, pokud se shoduje s rozsahem požadovaným klientem, jinak POVINNÉ|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|VOLITELNÉ. Rozsah žádosti o přístup.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|NEPOVINNÉ, pokud se shoduje s rozsahem požadovaným klientem, jinak POVINNÉ|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|VOLITELNÉ. Rozsah žádosti o přístup.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|NEPOVINNÉ, pokud se shoduje s rozsahem požadovaným klientem, jinak POVINNÉ|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|VOLITELNÉ. Rozsah žádosti o přístup.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|NEPOVINNÉ, pokud se shoduje s rozsahem požadovaným klientem, jinak POVINNÉ|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|POVINNÉ, pokud parametr "stavu" nacházel v žádosti o ověření klienta.  O přesnou hodnotu přijatou od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|DOPORUČENÉ. Neprůhledná hodnota používaných klientem pro uchování stavu mezi žádostí a zpětného volání.  Autorizační server používá tuto hodnotu při přesměrování uživatelského agenta zpět do klienta.  Parametr by měla sloužit jak zabránit padělání žádosti více webů.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|POVINNÉ, pokud parametr "stavu" nacházel v žádosti o ověření klienta.  O přesnou hodnotu přijatou od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|POVINNÉ, pokud parametr "stavu" nacházel v žádosti o ověření klienta.  O přesnou hodnotu přijatou od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|DOPORUČENÉ. Neprůhledná hodnota používaných klientem pro uchování stavu mezi žádostí a zpětného volání.  Autorizační server používá tuto hodnotu při přesměrování uživatelského agenta zpět do klienta.  Parametr by měla sloužit jak zabránit padělání žádosti více webů.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|POVINNÉ, pokud parametr "stavu" nacházel v žádosti o ověření klienta.  O přesnou hodnotu přijatou od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|POVINNÉ. Typ vystaveného tokenu.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|POVINNÉ. Typ vystaveného tokenu.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|POVINNÉ. Typ vystaveného tokenu.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|POVINNÉ. Typ vystaveného tokenu.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|POVINNÉ. Uživatelské jméno vlastníka prostředku.|  
|OAuth2UnsupportedTokenType|Token typu '{0}' není podporován.|  
|OAuth2InvalidState|Neplatná odpověď od autorizačního serveru|  
|OAuth2GrantType_AuthorizationCode|Autorizační kód|  
|OAuth2GrantType_Implicit|Implicitní|  
|OAuth2GrantType_ClientCredentials|Přihlašovací údaje klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Heslo vlastníka prostředku|  
|WebDocumentation302Code|302 – Nalezeno|  
|WebDocumentation400Code|400 (chybná žádost)|  
|OAuth2SendingMethod_AuthHeader|Autorizační hlavička|  
|OAuth2SendingMethod_QueryParam|Parametr dotazu|  
|OAuth2AuthorizationServerGeneralException|Došlo k chybě při autorizaci přístupu přes {0}|  
|OAuth2AuthorizationServerCommunicationException|Připojení HTTP k autorizačnímu serveru nebylo možné navázat, nebo bylo neočekávaně zavřeno.|  
|WebDocumentationOAuth2GeneralErrorMessage|Došlo k neočekávané chybě|  
|AuthorizationServerCommunicationException|Došlo k výjimce komunikace autorizační server. Obraťte se prosím na správce.|  
|TextblockSubscriptionKeyHeaderDescription|Klíč předplatného, který poskytuje přístup k tomuto rozhraní API. Součástí vaší < href ='/ developer'\>profilu < /a\>.|  
|TextblockOAuthHeaderDescription|Přístupový token OAuth 2.0 získaný od < i\>{0}< /i\>. Podporované typy udělení: < můžu\>{1}< /i\>.|  
|TextblockContentTypeHeaderDescription|Typ média textu odeslaného do rozhraní API|  
|ErrorMessageApiNotAccessible|V tuto chvíli není dostupná rozhraní API, které se snažíte volat. Obraťte se vydavatele tohoto rozhraní API < href = "/ issues"\>tady < /a\>.|  
|ErrorMessageApiTimedout|Rozhraní API, které se snažíte volat, trvá déle než obvykle vrátit odpověď. Obraťte se vydavatele tohoto rozhraní API < href = "/ issues"\>tady < /a\>.|  
|BadRequestParameterExpected|""{0}' očekává se parametr "|  
|TooltipTextDoubleClickToSelectAll|Dvojitým kliknutím vyberete vše.|  
|TooltipTextHideRevealSecret|Zobrazit/skrýt|  
|ButtonLinkOpenConsole|Vyzkoušet|  
|SectionHeadingRequestBody|Text požadavku|  
|SectionHeadingRequestParameters|Parametry žádosti|  
|SectionHeadingRequestUrl|Adresa URL požadavku|  
|SectionHeadingResponse|Odpověď|  
|SectionHeadingRequestHeaders|Hlavičky požadavku|  
|FormLabelSubtextOptional|nepovinné|  
|SectionHeadingCodeSamples|Ukázky kódů|  
|TextblockOpenidConnectHeaderDescription|Token identifikátoru OpenID Connect získaný od < i\>{0}< /i\>. Podporované typy udělení: < můžu\>{1}< /i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Název|Text|  
|----------|----------|  
|LinkLabelBack|zpět|  
|LinkLabelHomePage|domovská stránka|  
|LinkLabelSendUsEmail|Pošlete nám e-mail|  
|PageTitleError|Při obsluze požadované stránky bohužel došlo k problému|  
|TextblockPotentialCauseIntermittentIssue|Může jít problém s přístupem k přerušovaným dat, který už pominul.|  
|TextblockPotentialCauseOldLink|Odkaz, na který jste klikli, může být zastaralý a už nesměřuje na správné místo.|  
|TextblockPotentialCauseTechnicalProblem|Může jít o technický problém na naší straně.|  
|TextblockPotentialSolutionRefresh|Zkuste aktualizovat stránku.|  
|TextblockPotentialSolutionStartOver|Začněte znovu z našich {0}.|  
|TextblockPotentialSolutionTryAgain|Přejděte {0} a zkuste akci provést znovu.|  
|TextReportProblem|{0} popisující, co došlo k chybě a podíváme na to, co nejdříve podíváme.|  
|TitlePotentialCause|Potenciální příčina|  
|TitlePotentialSolution|Možná jde jen o dočasný problém, takže můžete vyzkoušet pár věcí|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Název|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problémy|  
|WebIssuesNoActiveSubscriptions|Budete mít žádná aktivní předplatná. Musíte se k odběru produktu, který chcete nahlásit problém.|  
|WebIssuesNotSignin|Nejste přihlášení. Prosím {0} nahlásit problém nebo odeslat komentář.|  
|WebIssuesReportIssueButton|Nahlásit problém|  
|WebIssuesSignIn|Přihlášení|  
|WebIssuesStatusReportedBy|Stav: {0} &#124; hlášených {1}|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Název|Text|  
|----------|----------|  
|LinkLabelHomePage|domovská stránka|  
|LinkLabelSendUsEmail|pošlete nám prosím e-mail|  
|PageTitleNotFound|Je nám líto, ale nenašli jsme stránku, kterou hledáte|  
|TextblockPotentialCauseMisspelledUrl|Možná je chybně adresu URL Pokud jste ho zadali.|  
|TextblockPotentialCauseOldLink|Odkaz, na který jste klikli, může být zastaralý a už nesměřuje na správné místo.|  
|TextblockPotentialSolutionRetype|Zkuste adresu URL zadat znovu.|  
|TextblockPotentialSolutionStartOver|Začněte znovu z našich {0}.|  
|TextReportProblem|{0} popisující, co došlo k chybě a podíváme na to, co nejdříve podíváme.|  
|TitlePotentialCause|Potenciální příčina|  
|TitlePotentialSolution|Potenciální řešení|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Název|Text|  
|----------|----------|  
|WebProductsAgreement|Se přihlásíte k odběru {0} produktu, souhlasím s `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Podmínky použití|  
|WebProductsSubscribeButton|Přihlásit odběr|  
|WebProductsUsageLimitsHeader|Limity využití|  
|WebProductsYouAreNotSubscribed|Tento produkt máte předplacený.|  
|WebProductsYouRequestedSubscription|Požádali jste o předplatné tohoto produktu.|  
|ErrorYouNeedToAgreeWithLegalTerms|Před pokračováním musíte souhlasit s podmínkami použití.|  
|ButtonLabelAddSubscription|Přidat předplatné|  
|LinkLabelChangeSubscriptionName|změnit|  
|ButtonLabelConfirm|Potvrdit|  
|TextblockMultipleSubscriptionsCount|Máte {0} předplatná pro tento produkt:|  
|TextblockSingleSubscriptionsCount|Máte {0} předplatné tohoto produktu:|  
|TextblockSingleApisCount|Tento produkt obsahuje {0} rozhraní API:|  
|TextblockMultipleApisCount|Tento produkt obsahuje {0} rozhraní API:|  
|TextblockHeaderSubscribe|Předplatit produkt|  
|TextblockSubscriptionDescription|Nové předplatné se vytvoří takto:|  
|TextblockSubscriptionLimitReached|Byl dosažen limit předplatných.|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Název|Text|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Název|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Správci v tuto chvíli zakázali přihlašování.|  
|TextboxExternalIdentitiesSigninInvitation|Alternativně se můžete přihlásit přes|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Přihlásit se přes:|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Název|Text|  
|----------|----------|  
|PrincipalNotFound|Objekt zabezpečení nebyl nalezen nebo podpis je neplatný.|  
|ErrorSsoAuthenticationFailed|Jednotné přihlašování se nepodařilo ověřit.|  
|ErrorSsoAuthenticationFailedDetailed|Byl zadán neplatný token nebo nelze ověřit podpis.|  
|ErrorSsoTokenInvalid|Token jednotného přihlašování je neplatný.|  
|ValidationErrorSpecificEmailAlreadyExists|E-mailu "{0}" již zaregistrován.|  
|ValidationErrorSpecificEmailInvalid|E-mailu "{0}' je neplatný|  
|ValidationErrorPasswordInvalid|Heslo je neplatné. Opravte chyby a zkuste to znovu.|  
|PropertyTooShort|{0} je příliš krátké.|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Neplatná e-mailová adresa|  
|ValidationMessageNewPasswordConfirmationRequired|Potvrdit nové heslo|  
|ValidationErrorPasswordConfirmationRequired|Potvrzovací heslo je prázdné.|  
|WebAuthenticationEmailChangeNotice|E-mail s potvrzením změny je na cestě {0}. Postupujte podle pokynů v něm potvrďte nové e-mailovou adresu. Pokud v e-mailu do vaší doručené pošty v několika dalších minut nedorazí, Zkontrolujte prosím složku s nevyžádanými e-maily.|  
|WebAuthenticationEmailChangeNoticeHeader|Vaše žádost o změnu e-mailu byla úspěšně zpracována|  
|WebAuthenticationEmailChangeNoticeTitle|Byla vyžádána změna e-mailu|  
|WebAuthenticationEmailHasBeenRevertedNotice|Váš e-mail už existuje. Žádost byla stornována|  
|ValidationErrorEmailAlreadyExists|E-mail už existuje.|  
|ValidationErrorEmailInvalid|Neplatná e-mailová adresa|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|E-mail je povinný.|  
|WebAuthenticationErrorNoticeHeader|Chyba|  
|WebAuthenticationFieldLengthErrorMessage|{0} Maximální délka musí být. {1}|  
|TextboxLabelEmailFirstName|Jméno|  
|ValidationErrorFirstNameRequired|Křesní jméno je povinné.|  
|ValidationErrorFirstNameInvalid|Neplatné křestní jméno|  
|NoticeInvalidInvitationToken|Mějte prosím na paměti, že potvrzovací odkazy jsou platné pouze 48 hodin. Pokud jste pořád v tomto časovém rámci, ujistěte se prosím, že je odkaz správný. Pokud platnost odkazu vypršela, zopakujte prosím akci, kterou se snažíte potvrdit.|  
|NoticeHeaderInvalidInvitationToken|Neplatný token pozvánky|  
|NoticeTitleInvalidInvitationToken|Chyba při potvrzení|  
|WebAuthenticationLastNameInvalidErrorMessage|Neplatné příjmení|  
|TextboxLabelEmailLastName|Příjmení|  
|ValidationErrorLastNameRequired|Příjmení je povinné.|  
|WebAuthenticationLinkExpiredNotice|Vypršela platnost potvrzovacího odkazu dostali. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Odkaz na resetování hesla je neplatný nebo mu vypršela platnost.|  
|WebAuthenticationLinkExpiredNoticeTitle|Odkaz odeslán|  
|WebAuthenticationNewPasswordLabel|Nové heslo|  
|ValidationMessageNewPasswordRequired|Vyžaduje se nové heslo.|  
|TextboxLabelNotificationsSenderEmail|E-mail odesílatele oznámení|  
|TextboxLabelOrganizationName|Název organizace|  
|WebAuthenticationOrganizationRequiredErrorMessage|Název organizace je prázdný.|  
|WebAuthenticationPasswordChangedNotice|Vaše heslo bylo úspěšně aktualizováno.|  
|WebAuthenticationPasswordChangedNoticeTitle|Heslo bylo aktualizováno|  
|WebAuthenticationPasswordCompareErrorMessage|Hesla se neshodují.|  
|WebAuthenticationPasswordConfirmLabel|Potvrzení hesla|  
|ValidationErrorPasswordInvalidDetailed|Heslo je moc slabé.|  
|WebAuthenticationPasswordLabel|Heslo|  
|ValidationErrorPasswordRequired|Heslo je povinné.|  
|WebAuthenticationPasswordResetSendNotice|E-mail s potvrzením změny hesla je na cestě {0}. Postupujte podle pokynů v e-mailu, pokračujte v procesu změny hesla.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Vaše žádost o resetování hesla byla úspěšně zpracována|  
|WebAuthenticationPasswordResetSendNoticeTitle|Bylo vyžádáno resetování hesla|  
|WebAuthenticationRequestNotFoundNotice|Žádost nebyla nalezena.|  
|WebAuthenticationSenderEmailRequiredErrorMessage|E-mail odesílatele oznámení je prázdný.|  
|WebAuthenticationSigninPasswordLabel|Potvrďte prosím změnu zadáním hesla.|  
|WebAuthenticationSignupConfirmNotice|E-mail s potvrzením registrace je na cestě k {0}. < br /\> postupujte podle pokynů v e-mailu aktivovat svůj účet. < br /\> Pokud e-mail nedorazí do doručené pošty během několika minut, prosím Zkontrolujte složku s nevyžádanými e-maily.|  
|WebAuthenticationSignupConfirmNoticeHeader|Váš účet byl úspěšně vytvořen|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|E-mail s potvrzením registrace byl odeslán znovu|  
|WebAuthenticationSignupConfirmNoticeTitle|Účet se vytvořil.|  
|WebAuthenticationTokenRequiredErrorMessage|Token je prázdný.|  
|WebAuthenticationUserAlreadyRegisteredNotice|Zdá se, že uživatel tuto e-mailovou je už zaregistrovaný v systému. Pokud jste zapomněli svoje heslo, zkuste prosím obnovit nebo se obraťte na náš tým podpory.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Uživatel je už zaregistrovaný|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Registrace už proběhla|  
|ButtonLabelChangePassword|Změnit heslo|  
|ButtonLabelChangeAccountInfo|Změnit informace účtu|  
|ButtonLabelCloseAccount|Zavřít účet|  
|WebAuthenticationInvalidCaptchaErrorMessage|Zadaný text neodpovídá text v obrázku. Zkuste to prosím znovu.|  
|ValidationErrorCredentialsInvalid|E-mail nebo heslo je neplatné. Opravte chyby a zkuste to znovu.|  
|WebAuthenticationRequestIsNotValid|Žádost není platná.|  
|WebAuthenticationUserIsNotConfirm|Předtím, než se pokusíte přihlásit, prosím potvrďte svou registraci.|  
|WebAuthenticationInvalidEmailFormated|E-mailu je neplatný: {0}|  
|WebAuthenticationUserNotFound|Uživatel se nenašel.|  
|WebAuthenticationTenantNotRegistered|Váš účet patří do tenanta Azure Active Directory, který nemá autorizaci pro přístup na tento portál.|  
|WebAuthenticationAuthenticationFailed|Ověřování selhalo.|  
|WebAuthenticationGooglePlusNotEnabled|Ověřování selhalo. Pokud jste ověřovali aplikaci pak prosím požádejte správce, abyste měli jistotu, že ověřování Google je správně nakonfigurován.|  
|ValidationErrorAllowedTenantIsRequired|Povolený tenant je povinný.|  
|ValidationErrorTenantIsNotValid|Tenant Azure Active Directory "{0}' není platný.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Přihlaste se pomocí vaší {0} účtu|  
|WebAuthenticationUserLimitNotice|Tato služba dosáhla maximálního počtu povolených uživatelů. Prosím `<a href="mailto:{0}"\>contact the administrator</a\>` upgrade služby a opětovné povolení registrace uživatelů.|  
|WebAuthenticationUserLimitNoticeHeader|Registrace uživatelů zakázána|  
|WebAuthenticationUserLimitNoticeTitle|Registrace uživatelů zakázána|  
|WebAuthenticationUserRegistrationDisabledNotice|Registrace uživatelů byla zakázána správcem. Přihlaste se prosím pomocí externího zprostředkovatele identity.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registrace uživatelů zakázána|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registrace uživatelů zakázána|  
|WebAuthenticationSignupPendingConfirmationNotice|Abychom mohli dokončit vytvoření vašeho účtu potřebujeme ověřit vaši e-mailovou adresu. Poslali jsme e-mail na {0}. Postupujte podle pokynů v e-mailu si můžete účet aktivovat. Pokud e-mail během několika dalších minut nedorazí, Zkontrolujte prosím složku s nevyžádanými e-maily.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Našli jsme Nepotvrzený účet pro e-mailovou adresu {0}. K dokončení vytvoření vašeho účtu potřebujeme ověřit vaši e-mailovou adresu. Poslali jsme e-mail na {0}. Postupujte podle pokynů v e-mailu si můžete účet aktivovat. Pokud e-mail během několika dalších minut nedorazí, Zkontrolujte prosím složku s nevyžádanými e-maily|  
|WebAuthenticationSignupConfirmationAlmostDone|Už to skoro je!|  
|WebAuthenticationSignupConfirmationEmailSent|Poslali jsme e-mail na {0}. Postupujte podle pokynů v e-mailu si můžete účet aktivovat. Pokud e-mail během několika dalších minut nedorazí, Zkontrolujte prosím složku s nevyžádanými e-maily.|  
|WebAuthenticationEmailSentNotificationMessage|E-mail byl úspěšně odeslán do {0}|  
|WebAuthenticationNoAadTenantConfigured|Pro tuto službu není nakonfigurovaný žádný tenant Azure Active Directory.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Souhlasím `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Najdete v tématu `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Podmínky použití|  
|ValidationMessageConsentNotAccepted|Před pokračováním musíte souhlasit s podmínkami použití.|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Název|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Zapomněli jste heslo?|  
|WebAuthenticationIfAdministrator|Pokud jste správce musíte přihlásit `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Ještě nejste členem? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Zapamatovat si mě na tomto počítači|  
|WebAuthenticationSigininWithPassword|Přihlaste se pomocí uživatelského jména a hesla.|  
|WebAuthenticationSigninTitle|Přihlášení|  
|WebAuthenticationSignUpNow|Zaregistrujte se|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Název|Text|  
|----------|----------|  
|PageTitleSignup|Registrace|  
|WebAuthenticationAlreadyAMember|Už jste členem?|  
|WebAuthenticationCreateNewAccount|Vytvořit nový účet API Managementu|  
|WebAuthenticationSigninNow|Přihlásit se|  
|ButtonLabelSignup|Registrace|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Název|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Opravdu chcete toto předplatné zrušit?|  
|SubscriptionRenewConfirmation|Opravdu chcete prodloužit platnost tohoto předplatného?|  
|WebDevelopersManageSubscriptions|Správa předplatných|  
|WebDevelopersPrimaryKey|Primární klíč|  
|WebDevelopersRegenerateLink|Znovu vytvořit|  
|WebDevelopersSecondaryKey|Sekundární klíč|  
|ButtonLabelShowKey|Zobrazit|  
|ButtonLabelRenewSubscription|Obnovit|  
|WebDevelopersSubscriptionRequested|Datum žádosti {0}|  
|WebDevelopersSubscriptionRequestedState|Požadováno|  
|WebDevelopersSubscriptionTableNameHeader|Název|  
|WebDevelopersSubscriptionTableStateHeader|Stav|  
|WebDevelopersUsageStatisticsLink|Sestavy Analytics|  
|WebDevelopersYourSubscriptions|Vaše předplatná|  
|SubscriptionPropertyLabelRequestedDate|Datum žádosti|  
|SubscriptionPropertyLabelStartedDate|Spuštěno dne|  
|PageTitleRenameSubscription|Přejmenovat předplatné|  
|SubscriptionPropertyLabelName|Název předplatného|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Název|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Chcete zavřít svůj účet?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Skrýt|  
|ButtonLabelRegenerateKey|Znovu vytvořit|  
|InformationMessageKeyWasRegenerated|Opravdu chcete znovu vygenerovat tento klíč?|  
|ButtonLabelShowKey|Zobrazit|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Název|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizovat profil|  
|PageTitleUpdateProfile|Aktualizovat informace o účtu|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|Název|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Změnit informace účtu|  
|ButtonLabelChangePassword|Změnit heslo|  
|ButtonLabelCloseAccount|Zavřít účet|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Jméno|  
|TextboxLabelEmailLastName|Příjmení|  
|TextboxLabelNotificationsSenderEmail|E-mail odesílatele oznámení|  
|TextboxLabelOrganizationName|Název organizace|  
|SubscriptionStateActive|Aktivní|  
|SubscriptionStateCancelled|Zrušeno|  
|SubscriptionStateExpired|Vypršela platnost|  
|SubscriptionStateRejected|Zamítnuto|  
|SubscriptionStateRequested|Požadováno|  
|SubscriptionStateSuspended|Pozastaveno|  
|DefaultSubscriptionNameTemplate|{0}  (výchozí)|  
|SubscriptionNameTemplate|# Přístup pro vývojáře{0}|  
|TextboxLabelSubscriptionName|Název předplatného|  
|ValidationMessageSubscriptionNameRequired|Název předplatného nemůže být prázdný.|  
|ApiManagementUserLimitReached|Tato služba dosáhla maximálního počtu povolených uživatelů. Upgradujte na vyšší cenovou úroveň.|  
  
##  <a name="glyphs"></a> Piktogram prostředky  
 Šablon portálu pro vývojáře API Management můžete použít glyfy z [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons). Tato sada glyfy obsahuje více než 250 glyfů ve formátu písmo z [Glyphicon](https://glyphicons.com/) Halflings nastavit. Použití piktogram z této sady, použijte následující syntaxi.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Úplný seznam glyfy, naleznete v tématu [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
