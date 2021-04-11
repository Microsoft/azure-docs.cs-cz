---
title: Ověřování Azure AD & kódy chyb autorizace
description: Přečtěte si o kódech chyb AADSTS, které se vracejí ze služby tokenů zabezpečení Azure AD (STS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 03/17/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 75d838e8ce785a4f59b056bf0dec736b982ea716
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283809"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Kódy chyb ověřování a autorizace Azure AD

Hledáte informace o kódech chyb AADSTS, které vrací služba tokenů zabezpečení Azure Active Directory (Azure AD)? Přečtěte si tento dokument, kde najdete popis chyb AADSTS, opravy a některá navrhovaná řešení.

> [!NOTE]
> Tyto informace jsou předběžné a můžou se změnit. Máte dotaz nebo nemůžete najít, co hledáte? Vytvořte problém GitHubu nebo si přečtěte nápovědu [a možnosti pomoci pro vývojáře](./developer-support-help-options.md) , kde se dozvíte další informace o tom, jak můžete získat nápovědu a podporu.
>
> Tato dokumentace je k dispozici pro vývojáře a pokyny pro správce, ale nikdy by neměla být používána samotným klientem. Kódy chyb se mohou kdykoli změnit, aby byly k dispozici podrobnější chybové zprávy, které mají pomáhat vývojářům při sestavování aplikace. Aplikace, které přijímají závislost na text nebo čísla chybových kódů, budou v průběhu času přerušeny.

## <a name="handling-error-codes-in-your-application"></a>Zpracování kódů chyb v aplikaci

[Specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-5.2) poskytuje pokyny, jak zpracovávat chyby při ověřování pomocí `error` části chybové odpovědi. 

Tady je Ukázková chybová odpověď:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parametr         | Popis    |
|-------------------|----------------|
| `error`       | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a měl by být použit k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. Nikdy nepoužívejte toto pole k reakci na chybu ve vašem kódu. |
| `error_codes` | Seznam chybových kódů specifických pro službu STS, které mohou být užitečné při diagnostice.  |
| `timestamp`   | Čas, kdy došlo k chybě. |
| `trace_id`    | Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice. |
| `correlation_id` | Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice napříč komponentami. |
| `error_uri` |  Odkaz na stránku vyhledávání chyb s dalšími informacemi o chybě.  Toto je jenom pro vývojáře, ale neprezentuje je uživatelům.  K dispozici pouze v případě, že systém vyhledávání chyb obsahuje další informace o chybě – ne všechny chyby mají k dispozici další informace.|

`error`Pole má několik možných hodnot – Přečtěte si odkazy na dokumentaci k protokolu a specifikace OAuth 2,0, abyste se dozvěděli víc o specifických chybách (například `authorization_pending` v [toku kódu zařízení](v2-oauth2-device-code.md)) a jak na ně reagovat.  Zde jsou uvedeny některé běžné položky:

| Kód chyby         | Description        | Akce klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Chyba protokolu, například chybějící požadovaný parametr. | Opravte a odešlete požadavek znovu.|
| `invalid_grant`    | Některé materiály pro ověřování (ověřovací kód, token aktualizace, přístupový token, PKCE) byly neplatné, neanalyzovatelné, chybějící nebo jinak nepoužitelné. | Vyzkoušejte nový požadavek na `/authorize` koncový bod, abyste získali nový autorizační kód.  Zvažte možnost zkontrolovat a ověřit používání protokolů v této aplikaci. |
| `unauthorized_client` | Ověřený klient nemá autorizaci k použití tohoto autorizačního typu udělení. | K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| `invalid_client` | Ověření klienta se nezdařilo.  | Pověření klienta nejsou platná. Chcete-li opravit, správce aplikace aktualizuje pověření.   |
| `unsupported_grant_type` | Autorizační Server nepodporuje typ udělení autorizace. | Změňte typ udělení v žádosti. Tento typ chyby by měl nastat pouze během vývoje a zjištěn při počátečním testování. |
| `invalid_resource` | Cílový prostředek není platný, protože neexistuje, služba Azure AD ho nemůže najít nebo není správně nakonfigurovaná. | To znamená, že pokud tento prostředek existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD.  Během vývoje se to obvykle označuje jako nesprávně nastavený tenant testu nebo překlep v názvu požadovaného oboru. |
| `interaction_required` | Požadavek vyžaduje zásah uživatele. Například je vyžadován další krok ověřování. | Opakujte požadavek se stejným prostředkem interaktivně, aby mohl uživatel dokončit všechny požadované výzvy.  |
| `temporarily_unavailable` | Server je dočasně zaneprázdněný pro zpracování žádosti. | Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |

## <a name="lookup-current-error-code-information"></a>Vyhledat aktuální informace o kódu chyby
Chybové kódy a zprávy mohou být změněny.  Nejaktuálnější informace najdete na stránce, kde [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) najdete popis chyb AADSTS, opravy a některá navrhovaná řešení.  

Pokud například obdržíte kód chyby "AADSTS50058", proveďte hledání [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) "50058".  Můžete také přímo propojit s konkrétní chybou přidáním čísla kódu chyby k adrese URL: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>Kódy chyb AADSTS

| Chyba | Popis |
|---|---|
| AADSTS16000 | SelectUserAccount – Toto je přerušení vyvolané službou Azure AD, které vede k uživatelskému rozhraní, které umožňuje uživateli vybrat z několika platných relací jednotného přihlašování. Tato chyba je poměrně častá a může se vrátit do aplikace, pokud `prompt=none` je zadaná. |
| AADSTS16001 | UserAccountSelectionInvalid – Tato chyba se zobrazí, pokud uživatel klikne na dlaždici, že byla logika výběru relace odmítnuta. Když se aktivuje Tato chyba, umožní uživateli obnovení z aktualizovaného seznamu dlaždic nebo relací nebo výběrem jiného účtu. K této chybě může dojít z důvodu vady kódu nebo stavu časování. |
| AADSTS16002 | AppSessionSelectionInvalid – požadavek na SID zadaný aplikací nebyl splněn.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant – označuje, že se uživatel nepřidal explicitně do tenanta. |
| AADSTS17003 | CredentialKeyProvisioningFailed – Azure AD nemůže zřídit klíč uživatele. |
| AADSTS20001 | WsFedSignInResponseError – došlo k potížím s vaším poskytovatelem federované identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS20012 | WsFedMessageInvalid – došlo k potížím s vaším poskytovatelem federované identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS20033 | FedMetadataInvalidTenantName – došlo k potížím s vaším poskytovatelem federované identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40008 | OAuth2IdPUnretryableServerError – došlo k potížím s vaším poskytovatelem federované identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError – došlo k potížím s vaším poskytovatelem federované identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40010 | OAuth2IdPRetryableServerError – došlo k potížím s vaším poskytovatelem federované identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError – došlo k potížím s vaším poskytovatelem federované identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS50000 | TokenIssuanceError – došlo k potížím s přihlašovací službou. Pokud chcete tento problém vyřešit, [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50001 | InvalidResource – prostředek je zakázaný nebo neexistuje. Zkontrolujte kód vaší aplikace a ujistěte se, že jste zadali přesnou adresu URL prostředku pro prostředek, ke kterému se pokoušíte získat přístup.  |
| AADSTS50002 | NotAllowedTenant – přihlášení nebylo úspěšné kvůli omezenému přístupu k proxy serveru v tenantovi. Pokud se jedná o vaše vlastní zásady tenanta, můžete tento problém vyřešit změnou nastavení zakázaného tenanta. |
| AADSTS500021 | Přístup k tenantovi {tenant} je odepřený. AADSTS500021 označuje, že je nakonfigurovaná funkce omezení tenanta a že se uživatel pokouší získat přístup k tenantovi, který není v seznamu povolených tenantů uvedených v hlavičce `Restrict-Access-To-Tenant` . Další informace najdete v tématu [použití omezení tenanta ke správě přístupu k SaaS cloudovým aplikacím](../manage-apps/tenant-restrictions.md).|
| AADSTS50003 | MissingSigningKey – přihlášení nebylo úspěšné, protože chybí podpisový klíč nebo certifikát. To může být způsobeno tím, že v aplikaci nebyl nakonfigurován žádný podpisový klíč. Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS50003](/troubleshoot/azure/active-directory/error-code-aadsts50003-cert-or-key-not-configured). Pokud se pořád zobrazují problémy, obraťte se na vlastníka aplikace nebo správce aplikace. |
| AADSTS50005 | DevicePolicyError – uživatel se pokusil přihlásit k zařízení z platformy, která není aktuálně podporovaná pomocí zásad podmíněného přístupu. |
| AADSTS50006 | Ověření podpisu InvalidSignature se nezdařilo kvůli neplatnému podpisu. |
| AADSTS50007 | PartnerEncryptionCertificateMissing – pro tuto aplikaci se nenašel certifikát pro šifrování partnerského serveru. [Otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s Microsoftem, abyste mohli tento problém vyřešit. |
| AADSTS50008 | InvalidSamlToken – kontrolní výraz SAML v tokenu chybí nebo je nesprávně nakonfigurovaný. Obraťte se na svého federačního zprostředkovatele. |
| AADSTS50010 | AudienceUriValidationFailed – ověření identifikátoru URI cílové skupiny pro aplikaci se nezdařilo, protože nebyly konfigurovány žádné cílové skupiny tokenů. |
| AADSTS50011 | InvalidReplyTo – adresa pro odpověď chybí, není správně nakonfigurovaná nebo neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci.  Jako řešení zajistěte, aby se tato chybějící adresa pro odpověď přidala do aplikace Azure Active Directory nebo aby měla osoba s oprávněním ke správě vaší aplikace ve službě Active Directory za vás. Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS50011](/troubleshoot/azure/active-directory/error-code-aadsts50011-reply-url-mismatch).|
| AADSTS50012 | AuthenticationFailed – ověření se nezdařilo z některého z následujících důvodů:<ul><li>Název předmětu podpisového certifikátu není autorizovaný.</li><li>Pro autorizovaný název subjektu se nenašly zásady odpovídajícího důvěryhodného úřadu.</li><li>Řetěz certifikátů není platný.</li><li>Podpisový certifikát není platný.</li><li>Zásada není nakonfigurovaná na tenantovi.</li><li>Kryptografický otisk podpisového certifikátu není autorizovaný.</li><li>Kontrolní výraz klienta obsahuje neplatný podpis.</li></ul> |
| AADSTS50013 | InvalidAssertion – kontrolní výraz je neplatný z důvodu různých důvodů – Vystavitel tokenu neodpovídá verzi rozhraní API v rámci jeho platného časového rozsahu – vypršela platnost – chybné tokeny Refresh v kontrolním výrazu není primární aktualizační token. |
| AADSTS50014 | GuestUserInPendingState – zaruč_cena uživatele je ve stavu čekání na vyřízení. Uživatelský účet hosta ještě není zcela vytvořen. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState – uživatel vyžaduje souhlas s právními předpisy pro věkovou skupinu. |
| AADSTS50017 | CertificateValidationFailed – ověření certifikace se nezdařilo, důvody z následujících důvodů:<ul><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Nepodařilo se najít očekávaný CrlSegment.</li><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Distribuční bod rozdílového seznamu CRL je nakonfigurovaný bez odpovídajícího distribučního bodu CRL.</li><li>Kvůli problému s časovým limitem se nepovedlo načíst platné segmenty seznamu CRL.</li><li>CRL nejde stáhnout.</li></ul>Obraťte se na správce tenanta. |
| AADSTS50020 | UserUnauthorized – uživatelé nejsou oprávněni volat tento koncový bod. |
| AADSTS50027 | InvalidJwtToken – neplatný token JWT z následujících důvodů:<ul><li>Neobsahuje deklaraci identity hodnoty nonce nebo subjektu.</li><li>Identifikátor subjektu se neshoduje.</li><li>Deklarace identity idTokenu má duplicitní deklaraci identity.</li><li>Neočekávaný vystavitel</li><li>Neočekávaná cílová skupina</li><li>Nenachází se v platném časovém rozsahu. </li><li>Nesprávný formát tokenu</li><li>U externího ID tokenu od vystavitele se nezdařilo ověření podpisu.</li></ul> |
| AADSTS50029 | Neplatný identifikátor URI – název domény obsahuje neplatné znaky. Obraťte se na správce tenanta. |
| AADSTS50032 | WeakRsaKey – určuje, že se chybný uživatel pokusí použít slabý klíč RSA. |
| AADSTS50033 | RetryableError – označuje přechodnou chybu, která nesouvisí s databázovými operacemi. |
| AADSTS50034 | UserAccountNotFound – Chcete-li se přihlásit k této aplikaci, musí být účet přidán do adresáře. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-v zásadě chybí hodnota Salt požadovaná pro vygenerování identifikátoru párových ID. Obraťte se na správce tenanta. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer – předmět nesouhlasí deklaraci identity vystavitele v kontrolním výrazu klienta. Obraťte se na správce tenanta. |
| AADSTS50049 | NoSuchInstanceForDiscovery – neznámá nebo neplatná instance. |
| AADSTS50050 | MalformedDiscoveryRequest – žádost je poškozená. |
| AADSTS50053 | IdsLocked – účet je zamčený, protože uživatel se pokusil o přihlášení příliš mnohokrát pomocí nesprávného ID uživatele nebo hesla. |
| AADSTS50055 | InvalidPasswordExpiredPassword – platnost hesla vypršela. |
| AADSTS50056 | Heslo pro tohoto uživatele v úložišti neexistuje nebo je neplatné nebo má hodnotu null. |
| AADSTS50057 | UserDisabled – uživatelský účet je zakázán. Tento účet byl zakázán správcem. |
| AADSTS50058 | UserInformationNotProvided – znamená, že uživatel není přihlášený. Jedná se o běžnou chybu, která se očekává, že uživatel není ověřený a ještě není přihlášený.</br>Pokud k této chybě dojde v kontextu jednotného přihlašování (SSO), kde se uživatel dřív přihlásil, znamená to, že relace jednotného přihlašování nebyla nalezena nebo je neplatná.</br>Tato chyba se může vrátit do aplikace, pokud je nastavená možnost Prompt = None. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided – informace identifikující tenant se v požadavku nenašly ani nevedly v zadaných přihlašovacích údajích. Uživatel může požádat správce tenanta, aby vám tento problém mohl vyřešit. |
| AADSTS50061 | SignoutInvalidRequest – žádost o odhlášení není platná. |
| AADSTS50064 | CredentialAuthenticationError – ověření přihlašovacích údajů u uživatelského jména nebo hesla se nezdařilo. |
| AADSTS50068 | SignoutInitiatorNotParticipant – odhlášení se nezdařilo. Aplikace, která iniciovala odhlášení, není účastníkem aktuální relace. |
| AADSTS50070 | SignoutUnknownSessionIdentifier – odhlášení se nezdařilo. Požadavek odhlášení zadal identifikátor názvu, který neodpovídá existujícím relacím. |
| AADSTS50071 | SignoutMessageExpired – vypršela platnost žádosti o odhlášení. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt – uživatel se musí zaregistrovat pro druhé faktor ověřování (Interactive). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt – vyžaduje se silné ověřování a uživatel nevyhověl při ověřování MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired – z důvodu změny konfigurace provedené správcem nebo z důvodu přesunutí do nového umístění musí uživatel k přístupu k prostředku použít službu Multi-Factor Authentication. Opakujte akci s novým požadavkem na autorizaci pro daný prostředek. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired – z důvodu změny konfigurace provedené správcem nebo proto, že uživatel přesunul do nového umístění, je nutné, aby uživatel používal službu Multi-Factor Authentication. |
| AADSTS50085 | Obnovovací token vyžaduje přihlášení prostřednictvím distribučního bodu vydávání (IDP) sociální sítě. Požádejte uživatele, aby se zkusil pomocí uživatelského jména a hesla přihlásit znovu. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError – služba je dočasně nedostupná. Zkuste to ještě jednou. |
| AADSTS50089 | Platnost tokenu toku vypršela – ověření se nezdařilo. Přihlaste se, aby se uživatel pokusil znovu přihlásit pomocí uživatelského jména a hesla. |
| AADSTS50097 | DeviceAuthenticationRequired – vyžaduje se ověřování zařízení. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized – podpis JWT je neplatný. |
| AADSTS50105 | EntitlementGrantsNotFound – přihlášený uživatel není přiřazen k roli pro přihlášenou aplikaci. Přiřaďte uživatele k aplikaci. Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS50105](/troubleshoot/azure/active-directory/error-code-aadsts50105-user-not-assigned-role). |
| AADSTS50107 | InvalidRealmUri – požadovaný objekt federační sféry neexistuje. Obraťte se na správce tenanta. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat – problém s hlavičkou JWT Obraťte se na správce tenanta. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter – transformace deklarací identity obsahuje neplatný vstupní parametr. Obraťte se na správce klienta, aby aktualizovat zásady. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt – přihlášení bylo přerušeno kvůli resetování hesla nebo zápisu hesla. |
| AADSTS50126 | InvalidUserNameOrPassword – Chyba při ověřování přihlašovacích údajů kvůli neplatnému uživatelskému jménu nebo heslu |
| AADSTS50127 | BrokerAppNotInstalled – uživatel musí nainstalovat aplikaci zprostředkovatele, aby získal přístup k tomuto obsahu. |
| AADSTS50128 | Neplatný název domény – nenašly se žádné informace zjištěné v žádosti ani žádné informace, které se nevedly v zadaných přihlašovacích údajích. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined – k registraci zařízení je potřeba připojení k síti na pracovišti. |
| AADSTS50131 | ConditionalAccessFailed – označuje různé chyby podmíněného přístupu, jako je například špatný stav zařízení s Windows, požadavek zablokovaný v důsledku podezřelé aktivity, zásad přístupu nebo rozhodnutí o zásadách zabezpečení. |
| AADSTS50132 | SsoArtifactInvalidOrExpired – relace není platná kvůli vypršení platnosti hesla nebo poslední změně hesla. |
| AADSTS50133 | SsoArtifactRevoked – relace není platná kvůli vypršení platnosti hesla nebo poslední změně hesla. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter – chybné datové centrum. Pokud chcete autorizovat požadavek, který iniciovala aplikace v toku zařízení OAuth 2,0, musí být autorizační strana ve stejném datovém centru, kde se nachází původní požadavek. |
| AADSTS50135 | PasswordChangeCompromisedPassword-Změna hesla se vyžaduje kvůli riziku účtu. |
| AADSTS50136 | Byla zjištěna relace RedirectMsaSessionToApp-Single MSA. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken – relace je neplatná z důvodu chybějícího externího aktualizačního tokenu. |
| AADSTS50140 | KmsiInterrupt – k této chybě došlo v důsledku přerušení přihlášení uživatele v případě, že se přihlásil uživatel. Více informací získáte, když [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
| AADSTS50143 | Neshoda relace – relace je neplatná, protože se tenant uživatele kvůli jinému prostředku neshoduje s nápovědou domény. Více informací získáte, když [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword – vypršela platnost hesla služby Active Directory uživatele. Vygenerujte nové heslo pro uživatele nebo ho použijte k resetování hesla pomocí nástroje pro samoobslužné obnovení. |
| AADSTS50146 | MissingCustomSigningKey – Tato aplikace musí být nakonfigurovaná pomocí podpisového klíče specifického pro aplikaci. Buď není pomocí tohoto klíče nakonfigurovaná nebo klíči vypršela platnost nebo ještě klíč v platnost nevstoupil. |
| AADSTS50147 | MissingCodeChallenge – velikost parametru výzvy kódu není platná. |
| AADSTS50155 | DeviceAuthenticationFailed – ověření zařízení pro tohoto uživatele se nezdařilo. |
| AADSTS50158 | ExternalSecurityChallenge – externí bezpečnostní výzvy nebyly splněny. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration – deklarace odeslané externím poskytovatelem nejsou dostatečné nebo chybí deklarace identity externímu poskytovateli. |
| AADSTS50166 | ExternalClaimsProviderThrottled – nepovedlo se odeslat požadavek zprostředkovateli deklarací. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired – klient může získat token jednotného přihlašování prostřednictvím rozšíření účtů Windows 10, ale token se v požadavku nenašel nebo vypršela platnost zadaného tokenu. |
| AADSTS50169 | InvalidRequestBadRealm – sféra není nakonfigurovanou sférou aktuálního oboru názvů služby. |
| AADSTS50170 | MissingExternalClaimsProviderMapping – chybí mapování externích ovládacích prvků. |
| AADSTS50173 | FreshTokenNeeded – poskytnutý grant vypršel z důvodu jeho odvolání a je potřeba nový ověřovací token. Správce nebo uživatel odvolal tokeny pro tohoto uživatele, což způsobilo selhání následné aktualizace tokenu a vyžaduje opakované ověření. Přihlaste se znovu. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers – externí výzva není pro průchozí uživatele podporována. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers – řízení relace není pro průchozí uživatele podporováno. |
| AADSTS50180 | WindowsIntegratedAuthMissing – vyžaduje se integrované ověřování systému Windows. Povolte tenantovi bezproblémové jednotné přihlašování. |
| AADSTS50187 | DeviceInformationNotProvided – službě se nepodařilo provést ověřování zařízení. |
| AADSTS50196 | LoopDetected – zjistila se smyčka klienta. Zkontrolujte logiku aplikace a ujistěte se, že je naimplementovaná mezipaměť tokenů a že se chybové stavy zpracovávají správně.  Aplikace provedla příliš krátkou řadu stejných požadavků v příliš krátkém období, což značí, že je v chybném stavu, nebo se jedná o urážlivý požadavek na tokeny. |
| AADSTS50197 | ConflictingIdentities – nepovedlo se najít uživatele. Zkuste se znovu přihlásit. |
| AADSTS50199 | CmsiInterrupt – z bezpečnostních důvodů je pro tento požadavek vyžadováno potvrzení uživatele.  Vzhledem k tomu, že se jedná o chybu "interaction_required", klient by měl provést interaktivní ověřování.  K tomu dochází, protože k vyžádání tokenu pro nativní aplikaci bylo použito systémové rozhraní WebView – uživatel musí být vyzván, aby požádal o to, jestli se jednalo o aplikaci, ke které se přihlašuje. Chcete-li se této výzvě vyhnout, identifikátor URI přesměrování by měl být součástí následujícího seznamu bezpečných: <br />http://<br />https://<br />msauth://(jenom iOS)<br />msauthv2://(jenom iOS)<br />Chrome – rozšíření://(jenom Prohlížeč Desktop Chrome) |
| AADSTS51000 | RequiredFeatureNotEnabled – Tato funkce je zakázaná. |
| AADSTS51001 | Pomocný parametr DomainHintMustbePresent-Domain musí být přítomen u místního identifikátoru zabezpečení nebo místního hlavního názvu uživatele (UPN). |
| AADSTS51004 | UserAccountNotInDirectory – uživatelský účet v adresáři neexistuje. |
| AADSTS51005 | TemporaryRedirect – ekvivalent stavu HTTP 307, který indikuje, že požadované informace jsou umístěné na identifikátoru URI, který je zadaný v hlavičce umístění. Když obdržíte tento stav, použijte hlavičku umístění přidruženou k odpovědi. Pokud byla metoda původní žádosti POST, přesměrovaný požadavek použije také metodu POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth – vyžaduje se integrované ověřování systému Windows. Uživatel přihlášený pomocí tokenu relace, u kterého chybí deklarace identity integrovaného ověřování systému Windows. Požádejte uživatele, aby se znovu přihlásil. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn – uživatel neposkytl souhlas pro přístup k prostředkům LinkedIn. |
| AADSTS53000 | DeviceNotCompliant – zásada podmíněného přístupu vyžaduje vyhovující zařízení a zařízení nedodržuje předpisy. Uživatel musí své zařízení zaregistrovat u schváleného zprostředkovatele správy mobilních zařízení, jako je Intune. |
| AADSTS53001 | DeviceNotDomainJoined – zásada podmíněného přístupu vyžaduje zařízení připojené k doméně a zařízení není připojené k doméně. Uživatel musí používat zařízení připojené k doméně. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp – použitá aplikace není schválenou aplikací pro podmíněný přístup. Aby uživatel mohl získat přístup, musí použít jednu z aplikací ze seznamu schválených aplikací. |
| AADSTS53003 | BlockedByConditionalAccess – zásady podmíněného přístupu zablokovaly přístup. Zásady přístupu nepovolují vystavení tokenu. |
| AADSTS53004 | ProofUpBlockedDueToRisk – uživatel musí před přístupem k tomuto obsahu dokončit proces registrace Multi-Factor Authentication. Uživatel by se měl zaregistrovat k vícefaktorovému ověřování. |
| AADSTS53011 | Uživatel zablokoval kvůli riziku pro domácího tenanta. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist – uživatel nebo správce nesouhlasí s používáním aplikace s ID X. odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek. |
| AADSTS65004 | UserDeclinedConsent – uživatel odmítl udělit souhlas s přístupem k aplikaci. Požádejte uživatele, aby se zkusil znovu přihlásit a udělil aplikaci souhlas.|
| AADSTS65005 | MisconfiguredApplication – seznam přístupu k prostředkům požadovaných aplikací neobsahuje aplikace, které prostředek zjistitelný, nebo klientská aplikace požadovala přístup k prostředku, který nebyl zadaný v požadovaném seznamu přístupu k prostředkům nebo služba Graph vrátila chybný požadavek nebo prostředek nebyl nalezen. Pokud aplikace podporuje SAML, možná jste nakonfigurovali aplikaci s nesprávným identifikátorem (entita). Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS650056](/troubleshoot/azure/active-directory/error-code-aadsts650056-misconfigured-app). |
| AADSTS650052 | Aplikace potřebuje přístup ke službě, ke `(\"{name}\")` které se vaše organizace `\"{organization}\"` nepřipojila nebo nepovolila. Požádejte správce IT, aby zkontroloval konfiguraci vašich předplatných služeb. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant – ověření se nezdařilo. Obnovovací token není platný. K chybě mohlo dojít z následujících důvodů:<ul><li>Hlavička vazby tokenu je prázdná.</li><li>Hodnota hash vazby tokenu se neshoduje.</li></ul> |
| AADSTS70001 | UnauthorizedClient – aplikace je zakázaná. Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS70001](/troubleshoot/azure/active-directory/error-code-aadsts70001-app-not-found-in-directory). |
| AADSTS70002 | InvalidClient – Chyba při ověřování přihlašovacích údajů. Zadaný client_secret se neshoduje s očekávanou hodnotou tohoto klienta. Opravte client_secret a zkuste to znovu. Další informace najdete v tématu [použití autorizačního kódu k vyžádání přístupového tokenu](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType – aplikace vrátila nepodporovaný typ udělení. |
| AADSTS70004 | InvalidRedirectUri – aplikace vrátila neplatný identifikátor URI přesměrování. Adresa přesměrování specifikovaná klientem neodpovídá žádné nakonfigurované adrese ani žádné adrese na seznamu schválených adres OIDC. |
| AADSTS70005 | UnsupportedResponseType – aplikace vrátila nepodporovaný typ odpovědi z následujících důvodů:<ul><li>typ odpovědi token není pro aplikaci povolený.</li><li>Typ odpovědi „id_token“ vyžaduje rozsah „OpenID“ – v parametru wctx obsahuje nepodporovanou hodnotu parametru OAuth.</li></ul> |
| AADSTS70007 | UnsupportedResponseMode – `response_mode` při požadavku na token vrátila aplikace nepodporovanou hodnotu.  |
| AADSTS70008 | ExpiredOrRevokedGrant – platnost obnovovacího tokenu vypršela z důvodu nečinnosti. Token byl vydán v XXX a byl po určitou dobu neaktivní. |
| AADSTS70011 | InvalidScope – rozsah požadovaný aplikací je neplatný. |
| AADSTS70012 | MsaServerError – při ověřování uživatele MSA (příjemce) došlo k chybě serveru. Zkuste to ještě jednou. Pokud k chybě dochází opakovaně, [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS70016 | Chyba toku zařízení AuthorizationPending-OAuth 2,0. Autorizace čeká na vyřízení. Zařízení se znovu pokusí dotazovat požadavek. |
| AADSTS70018 | BadVerificationCode – neplatný ověřovací kód z důvodu zadání nesprávného uživatelského kódu pro tok kódu zařízení uživatelem Autorizace není schválená. |
| AADSTS70019 | CodeExpired – platnost ověřovacího kódu vypršela. Nechejte uživatele opakovat přihlášení. |
| AADSTS75001 | BindingSerializationError – při vytváření vazby zprávy SAML došlo k chybě. |
| AADSTS75003 | UnsupportedBindingError – aplikace vrátila chybu související s nepodporovanou vazbou (odpověď protokolu SAML se nedá poslat přes jiné vazby než HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid – Azure AD nepodporuje požadavek SAML odeslaný aplikací pro jednotné přihlašování. Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS75005](/troubleshoot/azure/active-directory/error-code-aadsts75005-not-a-valid-saml-request). |
| AADSTS7500514 | Podporovaný typ odpovědi SAML nebyl nalezen. Podporované typy odpovědí jsou "Response" (v oboru názvů XML ' urn: Oasis: names: TC: SAML: 2.0: Protocol ') nebo ' assertion ' (v oboru názvů XML ' urn: Oasis: název: TC: SAML: 2.0: assertion '). Chyba aplikace – vývojář tuto chybu zpracuje.|
| AADSTS750054 | SAMLRequest nebo SAMLResponse musí být přítomné jako parametry řetězce dotazu v požadavku HTTP pro vazbu přesměrování SAML. Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS750054](/troubleshoot/azure/active-directory/error-code-aadsts750054-saml-request-not-present). |
| AADSTS75008 | RequestDeniedError – žádost z aplikace byla zamítnuta, protože žádost SAML měla neočekávaný cíl. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims – metoda ověřování, o kterou se uživatel s touto službou ověřil, se neshoduje s požadovanou metodou ověřování. Další informace najdete v článku věnovaném řešení potíží s chybou [AADSTS75011](/troubleshoot/azure/active-directory/error-code-aadsts75011-auth-method-mismatch). |
| AADSTS75016 | Požadavek na ověření Saml2AuthenticationRequestInvalidNameIDPolicy-typu Saml2 má neplatný NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable – ověřovací Agent se nemůže připojit ke službě Active Directory. Ujistěte se, že jsou servery agenta členy stejné doménové struktury služby AD, jako uživatelé, jejichž hesla je potřeba ověřit, a můžou se připojit ke službě Active Directory. |
| AADSTS80002 | Vypršel časový limit žádosti o ověření hesla OnPremisePasswordValidatorRequestTimedout. Ujistěte se, že je služba Active Directory k dispozici a reaguje na žádosti od agentů. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException – při zpracování odpovědi z ověřovacího agenta došlo k neznámé chybě. Opakujte požadavek. Pokud se i nadále nedaří, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) , kde získáte další informace o chybě. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem – ověřovací Agent nemůže ověřit heslo uživatele. Další informace najdete v protokolech agenta a ověřte, že služba Active Directory pracuje podle očekávání. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException – ověřovací Agent nemůže dešifrovat heslo. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours – uživatelé se pokusili o přihlášení mimo povolenou dobu (Tato možnost je určena ve službě AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew – pokus o ověření se nepovedlo dokončit kvůli časovému zkosení mezi počítačem, na kterém běží ověřovací agent a AD. Opravte problémy s synchronizací času. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated-pokus o ověření protokolu Kerberos se nezdařil. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported – balíček pro ověřování není podporován. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader – nenašla se žádná autorizační hlavička. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn – tenant není povolený pro bezproblémové přihlašování. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat – nepovedlo se ověřit lístek Kerberos uživatele. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid – bezproblémové jednotné přihlašování se nezdařilo, protože vypršela platnost lístku protokolu Kerberos uživatele nebo je neplatný. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed – nepovedlo se najít objekt uživatele na základě informací v lístku Kerberos uživatele. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn – uživatel, který se pokouší přihlásit ke službě Azure AD, se liší od uživatele přihlášeného k zařízení. |
| AADSTS90002 | InvalidTenantName – název tenanta se nenašel v úložišti dat. Ověřte, že máte správné ID tenanta. |
| AADSTS90004 | InvalidRequestFormat – požadavek nemá správný formát. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements – nepovedlo se dokončit požadavek. Požadavek není platný, protože identifikátor a pomocný parametr přihlášení nelze použít společně.  |
| AADSTS90006 | ExternalServerRetryableError – služba je dočasně nedostupná.|
| AADSTS90007 | InvalidSessionId – chybný požadavek. ID předané relace nelze analyzovat. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission – uživatel nebo správce nesouhlasí s používáním aplikace. Minimální je, že aplikace vyžaduje přístup ke službě Azure AD zadáním oprávnění pro přihlášení a čtení profilu uživatele. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier – aplikace požaduje token sám na sebe. Tento scénář je podporován pouze v případě, že zadaný prostředek používá ID aplikace založené na identifikátoru GUID. |
| AADSTS90010 | NotSupported – nepovedlo se vytvořit algoritmus. |
| AADSTS90012 | RequestTimeout – vypršel časový limit žádosti. |
| AADSTS90013 | InvalidUserInput – vstup od uživatele není platný. |
| AADSTS90014 | MissingRequiredField – tento kód chyby se může objevit v různých případech, když se v přihlašovacích údajích nezobrazí očekávané pole. |
| AADSTS90015 | QueryStringTooLong – řetězec dotazu je příliš dlouhý. |
| AADSTS90016 | MissingRequiredClaim – přístupový token není platný. Chybí požadovaná deklarace identity. |
| AADSTS90019 | MissingTenantRealm – službě Azure AD se nepovedlo určit identifikátor tenanta z požadavku. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat – formát hlavního názvu není platný nebo nesplňuje očekávaný `name[/host][@realm]` formát. Hlavní název je povinný, hostitel a sféra jsou volitelné a můžou být nastavené na hodnotu null. |
| AADSTS90023 | InvalidRequest – žádost ověřovací služby není platná. |
| AADSTS9002313 | InvalidRequest-žádost je poškozená nebo neplatná. – Problém je tady, protože u požadavku na určitý koncový bod došlo k nějaké chybě. K tomuto problému se dostanete tak, že získáte Fiddler trasování chyby a zjistíte, jestli je požadavek ve skutečnosti správně naformátovaný nebo ne. |
| AADSTS90024 | RequestBudgetExceededError – došlo k přechodné chybě. Zkuste to ještě jednou. |
| AADSTS90033 | MsodsServiceUnavailable – služba Microsoft online Directory Service (MSODS) není k dispozici. |
| AADSTS90036 | MsodsServiceUnretryableFailure – došlo k neočekávané chybě, která není znovu opakována ze služby WCF hostované službou MSODS. [Otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) pro získání dalších podrobností o chybě. |
| AADSTS90038 | NationalCloudTenantRedirection – zadaný tenant Y patří do národního cloudu X. Aktuální instance cloudu Z se federovat hodnotou X. Vrátí se chyba přesměrování cloudu. |
| AADSTS90043 | NationalCloudAuthCodeRedirection – Tato funkce je zakázaná. |
| AADSTS90051 | InvalidNationalCloudId – identifikátor národního cloudu obsahuje neplatný identifikátor cloudu. |
| AADSTS90055 | TenantThrottlingError – existuje příliš mnoho příchozích požadavků. Tato výjimka je vyvolána u blokovaných klientů. |
| AADSTS90056 | BadResourceRequest – Chcete-li uplatnit kód pro přístupový token, aplikace by měla poslat požadavek POST na `/token` koncový bod. Před tímto kódem byste také měli poskytnout autorizační kód a odeslat ho do požadavku POST do `/token` koncového bodu. V tomto článku najdete Přehled toku autorizačního kódu OAuth 2,0: [.. /azuread-dev/v1-Protocols-OAuth-Code.MD](../azuread-dev/v1-protocols-oauth-code.md). Nasměrujte uživatele na `/authorize` koncový bod, který vrátí authorization_code. Odesláním žádosti do `/token` koncového bodu uživatel získá přístupový token. Přihlaste se Azure Portal a zkontrolujte **koncové body Registrace aplikací >** , abyste zkontrolovali, jestli byly dva koncové body správně nakonfigurované. |
| AADSTS90072 | PassThroughUserMfaError – externí účet, ke kterému se uživatel přihlašuje, neexistuje v tenantovi, ke kterému se přihlásil. takže uživatel nemůže splnit požadavky MFA pro tenanta. K této chybě může dojít také v případě, že jsou uživatelé synchronizováni, ale v atributu ImmutableID (sourceAnchor) mezi službou Active Directory a službou Azure AD dojde k neshodě. Účet musí být nejdřív přidaný jako externí uživatel v tenantovi. Odhlaste se a přihlaste se pomocí jiného uživatelského účtu Azure AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid – došlo k chybě, když se služba pokusila zpracovat WS-Federationovou zprávu. Zpráva není platná. |
| AADSTS90082 | OrgIdWsFederationNotSupported – vybrané zásady ověřování pro požadavek se aktuálně nepodporují. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed – účty hostů nejsou pro tuto lokalitu povoleny. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed – služba nemůže vydat token, protože objekt společnosti nebyl dosud zřízen. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired – platnost tokenu DA uživatele vypršela. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed – při vytváření zprávy WS-Federation z identifikátoru URI došlo k chybě. |
| AADSTS90090 | GraphRetryableError – služba je dočasně nedostupná. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized – graf vrátil kód chyby zakázáno pro požadavek. |
| AADSTS90094 | AdminConsentRequired – vyžaduje se souhlas správce. |
| AADSTS900382 | Důvěrného klienta není v žádosti mezi cloudy podporován. |
| AADSTS90099 | Aplikace {appId} ({appName}) není v tenantovi {tenant} autorizovaná. Aplikace musí mít autorizaci pro přístup k tenantovi zákazníka, aby je mohli používat Delegovaní správci partnera. K autorizaci aplikace poskytněte příslušné rozhraní API partnerského centra nebo spusťte příslušné rozhraní API partnerského centra. |
| AADSTS900971| Nebyla zadána žádná adresa pro odpověď.|
| AADSTS90100 | InvalidRequestParameter – parametr je prázdný nebo neplatný. |
| AADSTS901002 | AADSTS901002: parametr žádosti prostředku není podporován. |
| AADSTS90101 | InvalidEmailAddress – zadaná data nejsou platnou e-mailovou adresou. E-mailová adresa musí být ve formátu `someone@example.com` . |
| AADSTS90102 | InvalidUriParameter – hodnota musí být platný absolutní identifikátor URI. |
| AADSTS90107 | InvalidXml – žádost není platná. Ujistěte se, že data neobsahují neplatné znaky.|
| AADSTS90114 | InvalidExpiryDate – časové razítko vypršení platnosti hromadného tokenu způsobí vydání tokenu, jehož platnost vypršela. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode – kód uživatele má hodnotu null nebo je prázdný.|
| AADSTS90120 | InvalidDeviceFlowRequest – žádost byla již autorizována nebo odmítnuta. |
| AADSTS90121 | InvalidEmptyRequest – neplatná prázdná žádost|
| AADSTS90123 | IdentityProviderAccessDenied – token se nedá vydat, protože poskytovatel vystavování identity nebo deklarace identity zamítl požadavek. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported – prostředek není podporován přes `/common` `/consumers` koncové body nebo. `/organizations`Místo toho použijte koncový bod nebo konkrétního klienta. |
| AADSTS90125 | DebugModeEnrollTenantNotFound – uživatel není v systému. Ujistěte se, že jste zadali správné uživatelské jméno. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred – typ uživatele není v tomto koncovém bodu podporován. Systém nemůže odvodit tenanta uživatele z uživatelského jména. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported – aplikace není podporována přes `/common` `/consumers` koncové body nebo. `/organizations`Místo toho použijte koncový bod nebo konkrétního klienta. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated – došlo k chybě bez opakování.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound – objekt zabezpečení uživatele nemá nakonfigurovaný klíč ID služby NGC. |
| AADSTS130005 | Signatura klíče NgcInvalidSignature-NGC se ověřila.|
| AADSTS130006 | NgcTransportKeyNotFound – transportní klíč služby NGC není na zařízení nakonfigurovaný. |
| AADSTS130007 | NgcDeviceIsDisabled – zařízení je zakázané. |
| AADSTS130008 | NgcDeviceIsNotFound – nepovedlo se najít zařízení, na které se odkazuje klíč NGC. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | Hodnota nonce InvalidRequestNonce-Request není zadána. |
| AADSTS140001 | InvalidSessionKey – klíč relace není platný.|
| AADSTS165900 | InvalidApiRequest – neplatný požadavek |
| AADSTS220450 | UnsupportedAndroidWebViewVersion – verze WebView pro Chrome není podporovaná. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource – prostředek není nakonfigurovaný tak, aby přijímal tokeny jenom pro zařízení. |
| AADSTS240001 | BulkAADJTokenUnauthorized – uživatel nemá autorizaci k registraci zařízení ve službě Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing – id_token nelze použít jako `urn:ietf:params:oauth:grant-type:jwt-bearer` udělení.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy – správce tenanta nakonfiguroval zásadu zabezpečení, která tento požadavek blokuje. Zkontrolujte zásady zabezpečení definované na úrovni tenanta a zjistěte, jestli váš požadavek splňuje požadavky zásad. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest – aplikace se nenašla v adresáři nebo tenantovi. K tomu může dojít v případě, že aplikace nebyla nainstalována správcem tenanta nebo nebyla odsouhlasena žádným uživatelem v tenantovi. Možná jste špatně nakonfigurovali hodnotu identifikátoru aplikace nebo jste odeslali požadavek na ověření do nesprávného tenanta. |
| AADSTS700020 | InteractionRequired – udělení přístupu vyžaduje interakci. |
| AADSTS700022 | InvalidMultipleResourcesScope – zadaná hodnota oboru vstupních parametrů není platná, protože obsahuje více než jeden prostředek. |
| AADSTS700023 | InvalidResourcelessScope – zadaná hodnota oboru vstupních parametrů není platná, pokud požaduje přístupový token. |
| AADSTS7000215 | Je zadaný neplatný tajný klíč klienta. Chyba vývojáře – aplikace se pokouší přihlásit bez správných parametrů ověřování.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided – platnost zadaných tajných klíčů klienta vypršela. Přejděte na Azure Portal pro vytvoření nových klíčů pro aplikaci nebo zvažte použití přihlašovacích údajů k certifikátu pro zvýšení zabezpečení: [https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | Autorizační kód poskytnutý InvalidGrantRedeemAgainstWrongTenant je určený pro použití s jiným klientem, tedy odmítnutý. Autorizační kód OAuth2 se musí uplatnit u stejného tenanta, kterého se získal pro (/běžné nebo/{tenant-ID} podle potřeby). |
| AADSTS1000000 | UserNotBoundError – rozhraní API pro vázání vyžaduje, aby uživatel Azure AD ověřil i s externím IDP, ke kterému ještě nedošlo. |
| AADSTS1000002 | BindCompleteInterruptError – vazba byla úspěšně dokončena, ale uživatel musí být informován. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled – aplikace je zakázaná. |
| AADSTS7000114| Aplikace ' appIdentifier ' nemá povoleno provádět aplikace na základě volání.|
| AADSTS7500529 | Hodnota ' SAMLId-GUID ' není platné ID SAML – Azure AD používá tento atribut k naplnění atributu InResponseTo vrácené odpovědi. ID nesmí začínat číslicí, takže běžnou strategií je předřadit řetězec jako "ID" do řetězcové reprezentace identifikátoru GUID. Například id6c1c178c166d486687be4aaf5e482730 je platný identifikátor. |

## <a name="next-steps"></a>Další kroky

* Máte dotaz nebo nemůžete najít, co hledáte? Vytvořte problém GitHubu nebo si přečtěte nápovědu [a možnosti pomoci pro vývojáře](./developer-support-help-options.md) , kde se dozvíte další informace o tom, jak můžete získat nápovědu a podporu.
