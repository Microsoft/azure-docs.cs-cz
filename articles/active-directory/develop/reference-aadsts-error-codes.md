---
title: Azure AD ověřování & chybové kódy autorizace
description: Další informace o chybové kódy AADSTS, které jsou vráceny ze služby tokenu zabezpečení Azure AD (STS).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/19/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 89240102837b65ed2a09d9f4865ad47ee5d5afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154555"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Chybové kódy ověřování azure a autorizace

Hledáte informace o kódech chyb AADSTS, které jsou vráceny ze služby tokenů zabezpečení Azure Active Directory (Azure AD) (STS)? Přečtěte si tento dokument a vyhledejte popisy chyb, opravy a některá navrhovaná řešení adabem AADSTS.

> [!NOTE]
> Tyto informace jsou předběžné a můžou se změnit. Máte dotaz nebo nemůžete najít, co hledáte? Vytvořte problém s GitHubem nebo si [přečtěte téma Podpora a možnosti nápovědy pro vývojáře,](active-directory-develop-help-support.md) kde se dozvíte o dalších způsobech, jak získat nápovědu a podporu.
>
> Tato dokumentace je k dispozici pro vývojáře a správce pokyny, ale by nikdy použít samotný klient. Kódy chyb se mohou kdykoli změnit, aby bylo možné poskytnout podrobnější chybové zprávy, které jsou určeny k pomoci vývojáři při vytváření jejich aplikace. Aplikace, které se závislost na text nebo čísla kódů chyb bude přerušena v průběhu času.

## <a name="lookup-current-error-code-information"></a>Vyhledávání aktuálních informací o kódu chyby
Kódy chyb a zprávy se mohou změnit.  Nejnovější informace naleznete na `https://login.microsoftonline.com/error` stránce a vyhledejte popisy chyb, opravy a některá navrhovaná řešení.  

Vyhledejte číselnou část vráceného kódu chyby.  Například pokud jste obdrželi kód chyby "AADSTS16000" pak proveďte vyhledávání v `https://login.microsoftonline.com/error` "16000".  Můžete také propojit přímo s konkrétní chybou přidáním čísla `https://login.microsoftonline.com/error?code=16000`kódu chyby do adresy URL: .

## <a name="aadsts-error-codes"></a>Chybové kódy AADSTS

| Chyba | Popis |
|---|---|
| AADSTS16000 | SelectUserAccount - Toto je přerušení vyvoláno Azure AD, což má za následek uživatelské rozhraní, které umožňuje uživateli vybrat z více platných relací přistaň. Tato chyba je poměrně časté a může `prompt=none` být vrácena do aplikace, pokud je zadán. |
| AADSTS16001 | UserAccountSelectionInvalid - Tato chyba se zobrazí, pokud uživatel klikne na dlaždici, kterou odmítla logika výběru relace. Při aktivaci tato chyba umožňuje uživateli obnovit výběrem z aktualizovaného seznamu dlaždic nebo relací nebo výběrem jiného účtu. K této chybě může dojít z důvodu vady kódu nebo spor. |
| AADSTS16002 | AppSessionSelectionInvalid - Požadavek SID zadaný aplikací nebyl splněn.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - Označuje, že uživatel nebyl explicitně přidán do klienta. |
| AADSTS17003 | CredentialKeyProvisioningFailed - Azure AD nelze zřídit uživatelský klíč. |
| AADSTS20001 | WsFedSignInResponseError - Došlo k problému s vaším federovaným poskytovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS20012 | WsFedMessageInvalid - Došlo k problému s vaším federovaným poskytovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS20033 | FedMetadataInvalidTenantName - Došlo k problému s vaším federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40008 | OAuth2IdPUnretryableServerError - Došlo k problému s vaším federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - Došlo k problému s vaším federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40010 | OAuth2IdPRetryableServerError - Došlo k problému s vaším federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - Došlo k problému s vaším federovaným zprostředkovatelem identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS50000 | TokenIssuanceError - Došlo k problému se službou přihlášení. Pokud chcete tento problém vyřešit, [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50001 | InvalidResource - Prostředek je zakázán nebo neexistuje. Zkontrolujte kód aplikace a ujistěte se, že jste zadali přesnou adresu URL prostředku pro prostředek, ke kterému se pokoušíte získat přístup.  |
| AADSTS50002 | NotAllowedTenant - Přihlášení se nezdařilo z důvodu omezeného přístupu k serveru proxy v tenantovi. Pokud se jedná o zásady vašeho vlastního tenanta, můžete změnit nastavení klienta s omezeným přístupem a tento problém vyřešit. |
| AADSTS50003 | Chybějící podpisový klíč - Přihlášení se nezdařilo z důvodu chybějícího podpisového klíče nebo certifikátu. Důvodem může být, že v aplikaci nebyl nakonfigurován žádný podpisový klíč. Podívejte se na usnesení [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured)uvedená na . Pokud se problémy stále zobrazují, obraťte se na vlastníka aplikace nebo správce aplikace. |
| AADSTS50005 | DevicePolicyError – Uživatel se pokusil přihlásit k zařízení z platformy, která momentálně není podporována prostřednictvím zásad podmíněného přístupu. |
| AADSTS50006 | Neplatný podpis - Ověření podpisu se nezdařilo z důvodu neplatného podpisu. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - Pro tuto aplikaci nebyl nalezen certifikát šifrování partnera. [Otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) se společností Microsoft, abyste to opravili. |
| AADSTS50008 | InvalidSamlToken - výraz SAML chybí nebo nesprávně nakonfiguroval v tokenu. Obraťte se na svého federačního zprostředkovatele. |
| AADSTS50010 | AudienceUriValidationFailed - Ověření uri publika pro aplikaci se nezdařilo, protože nebyly nakonfigurovány žádné cílové skupiny tokenů. |
| AADSTS50011 | InvalidReplyTo - Adresa odpovědi chybí, nesprávně nakonfigurovala nebo neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci.  Jako řešení: Ujistěte se, že chcete přidat tuto chybějící adresu odpovědi do aplikace Azure Active Directory nebo mít někoho s oprávněními ke správě vaší aplikace ve službě Active Directory, udělejte to za vás.|
| AADSTS50012 | Ověřování se nezdařilo - ověřování se nezdařilo z jednoho z následujících důvodů:<ul><li>Název subjektu podpisového certifikátu není autorizován.</li><li>Pro název autorizovaného subjektu nebyly nalezeny odpovídající zásady důvěryhodnéautority.</li><li>Řetěz certifikátů není platný.</li><li>Podpisový certifikát není platný.</li><li>Zásady nejsou nakonfigurovány v tenantovi.</li><li>Kryptografický otisk podpisového certifikátu není autorizován.</li><li>Kontrolní výraz klienta obsahuje neplatný podpis.</li></ul> |
| AADSTS50013 | InvalidAssertion - Assertion je neplatný z různých důvodů - vystavitel tokenu neodpovídá verzi rozhraní API v rámci platného časového rozsahu -expired -versionformed - Refresh token v kontrolním výrazu není primární obnovovací token. |
| AADSTS50014 | GuestUserInPendingState - Využití uživatele je ve stavu čekající na vyřízení. Uživatelský účet hosta ještě není plně vytvořen. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - Uživatel vyžaduje souhlas zákonné věkové skupiny. |
| AADSTS50017 | Ověření certifikátu Se nezdařilo - ověření certifikace se nezdařilo, důvody z následujících důvodů:<ul><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Nepodařilo se najít očekávaný CrlSegment.</li><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Distribuční bod rozdílového seznamu CRL je nakonfigurovaný bez odpovídajícího distribučního bodu CRL.</li><li>Nelze načíst platné segmenty seznamu odvolaných certifikátů z důvodu problému s časovým úsporeč.</li><li>CRL nejde stáhnout.</li></ul>Obraťte se na správce tenanta. |
| AADSTS50020 | UserUnauthorized - Uživatelé jsou neoprávněné volání tohoto koncového bodu. |
| AADSTS50027 | InvalidJwtToken - Neplatný token JWT z následujících důvodů:<ul><li>Neobsahuje deklaraci identity hodnoty nonce nebo subjektu.</li><li>Identifikátor subjektu se neshoduje.</li><li>Deklarace identity idTokenu má duplicitní deklaraci identity.</li><li>Neočekávaný vystavitel</li><li>Neočekávaná cílová skupina</li><li>Nenachází se v platném časovém rozsahu. </li><li>Nesprávný formát tokenu</li><li>U externího ID tokenu od vystavitele se nezdařilo ověření podpisu.</li></ul> |
| AADSTS50029 | Neplatný identifikátor URI – název domény obsahuje neplatné znaky. Obraťte se na správce tenanta. |
| AADSTS50032 | WeakRsaKey - Označuje chybný pokus uživatele o použití slabého klíče RSA. |
| AADSTS50033 | Opakovatelná chyba - Označuje přechodnou chybu, která nesouvisí s databázovými operacemi. |
| AADSTS50034 | UserAccountNotFound - Chcete-li se přihlásit do této aplikace, musí být účet přidán do adresáře. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - sůl potřebná ke generování párového identifikátoru v zásadě chybí. Obraťte se na správce tenanta. |
| AADSTS50043 | Nelzegenerovat pairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - Neshody předmětu Deklarace vystavitele v kontrolním výrazu klienta. Obraťte se na správce tenanta. |
| AADSTS50049 | NoSuchInstanceForDiscovery - Neznámá nebo neplatná instance. |
| AADSTS50050 | MalformedDiscoveryRequest - Požadavek je poškozen. |
| AADSTS50053 | IdsLocked - Účet je uzamčen, protože uživatel se pokusil přihlásit příliš mnohokrát s nesprávným ID uživatele nebo heslo. |
| AADSTS50055 | InvalidPasswordExpiredPassword - Platnost hesla vypršela. |
| AADSTS50056 | Neplatné nebo nulové heslo -Heslo pro tohoto uživatele v úložišti neexistuje. |
| AADSTS50057 | UserDisabled - Uživatelský účet je zakázán. Správce tento účet zakázal. |
| AADSTS50058 | UserInformationNotProvided - To znamená, že uživatel není přihlášen. Toto je běžná chyba, která se očekává, když je uživatel neověřený a ještě není přihlášen.</br>Pokud je tato chyba podporována v kontextu s přihlašování, kde se uživatel dříve přihlásil, znamená to, že relace s přihlašování nebyla nalezena nebo neplatná.</br>Tato chyba může být vrácena do aplikace, pokud prompt=none je zadán. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - Informace identifikující klienta nebyly nalezeny v požadavku ani implikované žádné zadaných pověření. Uživatel může kontaktovat správce klienta a pomoci vyřešit problém. |
| AADSTS50061 | SignoutInvalidRequest - Požadavek na odhlášení je neplatný. |
| AADSTS50064 | CredentialAuthenticationError - Ověření pověření u uživatelského jména nebo hesla se nezdařilo. |
| AADSTS50068 | SignoutInitiatorNotParticipant - Signout se nezdařilo. Aplikace, která iniciovala odhlášení, není účastníkem aktuální relace. |
| AADSTS50070 | SignoutUnknownSessionIdentifier - Signout se nezdařil. Požadavek na odhlášení zadal identifikátor názvu, který neodpovídá existující relaci(s). |
| AADSTS50071 | SignoutMessageExpired - Vypršela platnost požadavku na odhlášení. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - Uživatel potřebuje zaregistrovat pro druhé faktorové ověřování (interaktivní). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - Je vyžadováno silné ověřování a uživatel neprošel výzvou MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - Vzhledem ke změně konfigurace provedené správcem nebo proto, že jste se přestěhovali do nového umístění, musí uživatel pro přístup k prostředku použít vícefaktorové ověřování. Opakujte akci s novým požadavkem na autorizaci prostředku. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - Vzhledem ke změně konfigurace provedené správcem nebo proto, že uživatel přesunut do nového umístění, je uživatel povinen použít vícefaktorové ověřování. |
| AADSTS50085 | Obnovovací token vyžaduje přihlášení prostřednictvím distribučního bodu vydávání (IDP) sociální sítě. Požádejte uživatele, aby se zkusil pomocí uživatelského jména a hesla přihlásit znovu. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - Služba je dočasně nedostupná. Zkuste to znovu. |
| AADSTS50089 | Platnost tokenu toku vypršela – ověření se nezdařilo. Ať se uživatel pokusí znovu přihlásit pomocí uživatelského jména -password. |
| AADSTS50097 | DeviceAuthenticationRequired - je vyžadováno ověření zařízení. |
| AADSTS50099 | PKeyAuthInvalidJwtNeautorizováno - Podpis JWT je neplatný. |
| AADSTS50105 | EntitlementGrantsNotFound - Přihlášený uživatel není přiřazen k roli pro podepsané v aplikaci. Přiřaďte uživatele k aplikaci. Pro více[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)informací: . |
| AADSTS50107 | InvalidRealmUri - Požadovaný objekt sféry federace neexistuje. Obraťte se na správce tenanta. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - Problém s hlavičkou JWT. Obraťte se na správce tenanta. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - Claims Transformation obsahuje neplatný vstupní parametr. Obraťte se na správce klienta, aby aktualizovat zásady. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - Přihlášení bylo přerušeno z důvodu obnovení hesla nebo zadání registrace hesla. |
| AADSTS50126 | InvalidUserNameOrPassword - Chyba ověřování přihlašovacích údajů z důvodu neplatného uživatelského jména nebo hesla. |
| AADSTS50127 | BrokerAppNotInstalled - Uživatel potřebuje nainstalovat aplikaci broker získat přístup k tomuto obsahu. |
| AADSTS50128 | Neplatný název domény – v požadavku nebyly nalezeny žádné informace o identifikaci klienta ani žádné poskytnuté přihlašovací údaje. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined – připojení k pracovišti je nutné k registraci zařízení. |
| AADSTS50131 | Podmíněná přístupfailed - Označuje různé chyby podmíněného přístupu, jako je například špatný stav zařízení systému Windows, požadavek blokován z důvodu podezřelé aktivity, zásady přístupu nebo rozhodnutí zásad zabezpečení. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - Relace není platná z důvodu vypršení platnosti hesla nebo nedávné změny hesla. |
| AADSTS50133 | SsoArtifactRevoked - Relace není platná z důvodu vypršení platnosti hesla nebo nedávné změny hesla. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - Nesprávné datové centrum. Chcete-li autorizovat požadavek, který byl iniciován aplikací v toku zařízení OAuth 2.0, musí být autorizační strana ve stejném datovém centru, kde je původní požadavek umístěn. |
| AADSTS50135 | PasswordChangeCompromisedPassword - Změna hesla je vyžadována z důvodu rizika účtu. |
| AADSTS50136 | RedirectMsaSessionToApp - byla zjištěna jedna relace MSA. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - Relace je neplatná z důvodu chybějícího externího obnovovacího tokenu. |
| AADSTS50140 | KmsiInterrupt - K této chybě došlo z důvodu přerušení "Keep Me signed in" při přihlášení uživatele. Více informací získáte, když [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
| AADSTS50143 | Neshoda relace - Relace je neplatná, protože klient uživatele neodpovídá nápovědě domény z důvodu jiného prostředku.  [Chcete-li](../fundamentals/active-directory-troubleshooting-support-howto.md) získat další podrobnosti, otevřete lístek podpory s ID korelace, ID požadavku a kódem chyby. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - heslo služby Active Directory uživatele vypršelo. Vygenerujte nové heslo pro uživatele nebo chcete, aby uživatel použil samoobslužný nástroj pro resetování hesla. |
| AADSTS50146 | MissingCustomSigningKey – Tato aplikace musí být nakonfigurována s podpisovým klíčem specifickým pro aplikaci. Buď není pomocí tohoto klíče nakonfigurovaná nebo klíči vypršela platnost nebo ještě klíč v platnost nevstoupil. |
| AADSTS50147 | MissingCodeChallenge - Velikost parametru výzvy kódu není platná. |
| AADSTS50155 | DeviceAuthenticationFailed - Ověřování zařízení se nezdařilo pro tohoto uživatele. |
| AADSTS50158 | ExternalSecurityChallenge - Externí bezpečnostní výzva nebyla splněna. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - Deklarace odeslané externím zprostředkovatelem nestačí nebo chybějící deklarace požadovaná externímu zprostředkovateli. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Nepodařilo se odeslat požadavek zprostředkovateli deklarací identity. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - Klient je schopen získat token s přistupuje prostřednictvím rozšíření účty Windows 10, ale token nebyl nalezen v požadavku nebo zadaný token vypršela. |
| AADSTS50169 | InvalidRequestBadRealm - Sféra není nakonfigurovanou sférou aktuálního oboru názvů služby. |
| AADSTS50170 | MissingExternalClaimsProviderMapping - Chybí mapování externích ovládacích prvků. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - Externí výzva není podporována pro passthrough uživatele. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - Ovládací prvek relace není podporován pro předávací uživatele. |
| AADSTS50180 | WindowsIntegratedAuthMissing - integrované ověřování systému Windows je potřeba. Povolte tenantovi bezproblémové jednotné přihlašování. |
| AADSTS50187 | DeviceInformationNotProvided - službě se nepodařilo provést ověřování zařízení. |
| AADSTS50196 | LoopDetected - Byla zjištěna smyčka klienta. Zkontrolujte logiku aplikace a ujistěte se, že ukládání tokenů do mezipaměti je implementováno a že chybové stavy jsou zpracovány správně.  Aplikace učinila příliš mnoho stejného požadavku v příliš krátkém období, což znamená, že je v vadném stavu nebo je zneužívající požadavek na tokeny. |
| AADSTS50199 | CmsiInterrupt - Z bezpečnostních důvodů je pro tento požadavek vyžadováno potvrzení uživatele.  Vzhledem k tomu, že se jedná o chybu "interaction_required", klient by měl provést interaktivní ověřování.  K tomu dochází, protože systémwebové zobrazení byla použita k vyžádání tokenu pro nativní aplikace – uživatel musí být vyzváni k dotazu, zda to byla skutečně aplikace, které chtěl přihlásit.|
| AADSTS51000 | RequiredFeatureNotEnabled - Funkce je zakázána. |
| AADSTS51001 | DomainHintMustbePresent - Nápověda domény musí být k dispozici s identifikátorem místního zabezpečení nebo místní hlavní název sítě. |
| AADSTS51004 | UserAccountNotInDirectory - Uživatelský účet v adresáři neexistuje. |
| AADSTS51005 | Dočasné přesměrování – odpovídá stavu PROTOKOLU HTTP 307, což znamená, že požadované informace jsou umístěny v identifikátoru URI zadaném v hlavičce umístění. Když obdržíte tento stav, postupujte podle záhlaví umístění přidružené k odpovědi. Pokud byla původní metoda požadavku POST, přesměrovaný požadavek bude také používat metodu POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - Je potřeba integrované ověřování systému Windows. Uživatel přihlášen pomocí tokenu relace, který chybí integrované deklarace systému Windows ověřování. Požádejte uživatele, aby se znovu přihlásil. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - Uživatel neposkytl souhlas s přístupem k prostředkům LinkedIn. |
| AADSTS53000 | DeviceNotCompliant - zásady podmíněného přístupu vyžaduje kompatibilní zařízení a zařízení není kompatibilní. Uživatel musí zaregistrovat své zařízení u schváleného poskytovatele MDM, jako je Intune. |
| AADSTS53001 | DeviceNotDomainJoined – zásady podmíněného přístupu vyžaduje zařízení spojené s doménou a zařízení není připojeno k doméně. Vyzvěte uživatele, aby používal zařízení spojené s doménou. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - použitá aplikace není schválená aplikace pro podmíněný přístup. Uživatel musí použít jednu z aplikací ze seznamu schválených aplikací, které chcete použít, aby získali přístup. |
| AADSTS53003 | BlockedByConditionalAccess - Přístup byl zablokován zásadami podmíněného přístupu. Zásady přístupu neumožňuje vystavování tokenů. |
| AADSTS53004 | ProofUpBlockedDueToRisk - Uživatel musí před přístupem k tomuto obsahu dokončit proces registrace vícefaktorového ověřování. Uživatel by se měl zaregistrovat k vícefaktorovému ověřování. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - Uživatel nebo správce nesouhlasil s použitím aplikace s ID X. Odešle tes a odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek. |
| AADSTS65004 | UserDeclinedConsent – Uživatel odmítl souhlas s přístupem k aplikaci. Požádejte uživatele, aby se zkusil znovu přihlásit a udělil aplikaci souhlas.|
| AADSTS65005 | MisconfiguredApplication - Seznam požadovaných prostředků aplikace neobsahuje aplikace zjistitelné prostředkem nebo klientská aplikace požádala o přístup k prostředku, který nebyl zadán v seznamu požadovaných přístupů k prostředkům nebo služba Graph vrácena chybně požadavek nebo prostředek nebyl nalezen. Pokud aplikace podporuje SAML, je možné, že jste nakonfigurovali aplikaci s nesprávným identifikátorem (entitou). Vyzkoušejte rozlišení uvedené pro SAML pomocí následujícího odkazu:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - Ověřování se nezdařilo. Obnovovací token není platný. Chyba může být způsobena následujícími důvody:<ul><li>Hlavička vazby tokenu je prázdná.</li><li>Token vazby hash neodpovídá</li></ul> |
| AADSTS70001 | UnauthorizedClient - Aplikace je zakázána. |
| AADSTS70002 | Neplatný klient - Chyba ověřování pověření. Zadaný client_secret neodpovídá očekávané hodnotě pro tohoto klienta. Opravte client_secret a akci opakujte. Další informace najdete [v tématu Použití autorizačního kódu k vyžádání přístupového tokenu](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType - Aplikace vrátila nepodporovaný typ grantu. |
| AADSTS70004 | InvalidRedirectUri – aplikace vrátila neplatný identifikátor URI přesměrování. Adresa přesměrování specifikovaná klientem neodpovídá žádné nakonfigurované adrese ani žádné adrese na seznamu schválených adres OIDC. |
| AADSTS70005 | UnsupportedResponseType - Aplikace vrátila nepodporovaný typ odpovědi z následujících důvodů:<ul><li>typ odpovědi 'token' není povolen pro aplikaci</li><li>Typ odpovědi „id_token“ vyžaduje rozsah „OpenID“ – v parametru wctx obsahuje nepodporovanou hodnotu parametru OAuth.</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - Aplikace vrátila nepodporovanou hodnotu `response_mode` při vyžádání tokenu.  |
| AADSTS70008 | ExpiredOrRevokedGrant - platnost obnovovacího tokenu vypršela z důvodu nečinnosti. Token byl vydán na XXX a byl neaktivní po určitou dobu. |
| AADSTS70011 | InvalidScope – Obor požadovaný aplikací je neplatný. |
| AADSTS70012 | MsaServerError - Při ověřování uživatele MSA (příjemce) došlo k chybě serveru. Zkuste to znovu. Pokud se stále nezdaří, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - Chyba toku zařízení OAuth 2.0. Autorizace čeká na vyřízení. Zařízení bude opakovat dotazování požadavku. |
| AADSTS70018 | BadVerificationCode - Neplatný ověřovací kód z důvodu, že uživatel zadává nesprávný uživatelský kód pro tok kódu zařízení. Autorizace není schválena. |
| AADSTS70019 | CodeExpired - Ověřovací kód vypršel. Ať uživatel zopakujte přihlášení. |
| AADSTS75001 | BindingSerializationError - Během vazby zprávy SAML došlo k chybě. |
| AADSTS75003 | UnsupportedBindingError - Aplikace vrátila chybu související s nepodporovanou vazbou (odpověď protokolu SAML nelze odeslat prostřednictvím jiných vazeb než HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid – Azure AD nepodporuje požadavek SAML odeslaný aplikací pro službu SSO. |
| AADSTS75008 | RequestDeniedError - Požadavek z aplikace byl odepřen, protože požadavek SAML měl neočekávaný cíl. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - Metoda ověřování, kterou uživatel ověřený službou neodpovídá požadované metodě ověřování. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - POŽADAVEk ověřování SAML2 má neplatný nameidpolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - Agent ověřování se nemůže připojit ke službě Active Directory. Ujistěte se, že servery agentů jsou členy stejné doménové struktury služby AD jako uživatelé, jejichž hesla je třeba ověřit, a mohou se připojit ke službě Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - Požadavek na ověření hesla byl vypován. Ujistěte se, že služba Active Directory je k dispozici a odpovídá na požadavky od agentů. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - Při zpracování odpovědi agenta ověřování došlo k neznámé chybě. Opakujte požadavek. Pokud se nezdaří, [otevřete lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) získat další podrobnosti o chybě. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - Agent ověřování nemůže ověřit heslo uživatele. Další informace naleznete v protokolech agenta a ověřte, zda služba Active Directory funguje podle očekávání. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionEncryptionException - Agent ověřování nemůže dešifrovat heslo. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - Uživatelé se pokusili přihlásit mimo povolené hodiny (to je zadáno ve službě AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - Pokus o ověření nelze dokončit z důvodu časového zkosení mezi počítačem se systémem agenta ověřování a službou AD. Opravte problémy se synchronizací času. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Pokus o ověření protokolem Kerberos se nezdařil. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - Ověřovací balíček není podporován. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Nebyla nalezena žádná hlavička autorizace. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - tenant není povolena pro bezproblémové jednotné přihlašování. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - Nelze ověřit lístek protokolu Kerberos uživatele. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - Bezproblémové jednotné přihlašované místo se nezdařilo, protože vypršela platnost lístku protokolu Kerberos uživatele nebo je neplatný. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - Nelze najít objekt uživatele na základě informací v lístku protokolu Kerberos uživatele. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - Uživatel, který se pokouší přihlásit k Azure AD, se liší od uživatele přihlášeného k zařízení. |
| AADSTS90002 | InvalidTenantName - Název klienta nebyl nalezen v úložišti dat. Zkontrolujte, zda máte správné ID klienta. |
| AADSTS90004 | InvalidRequestFormat - Požadavek není správně formátován. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - Požadavek nelze dokončit. Požadavek není platný, protože identifikátor a nápovědu pro přihlášení nelze použít společně.  |
| AADSTS90006 | ExternalServerRetryableError - Služba je dočasně nedostupná.|
| AADSTS90007 | InvalidSessionId - Chybný požadavek. ID předané relace nelze analyzovat. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - Uživatel nebo správce nesouhlasil s použitím aplikace. Aplikace vyžaduje přístup k Azure AD minimálně zadáním oprávnění k přihlášení a čtení profilu uživatele. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - Aplikace požaduje token pro sebe. Tento scénář je podporován pouze v případě, že prostředek, který je zadán používá ID aplikace založené na GUID. |
| AADSTS90010 | NotSupported - Nelze vytvořit algoritmus. |
| AADSTS90012 | RequestTimeout - požadovaný časový čas. |
| AADSTS90013 | InvalidUserInput - Vstup od uživatele není platný. |
| AADSTS90014 | MissingRequiredField - Tento kód chyby se může zobrazit v různých případech, kdy v pověření není k dispozici očekávané pole. |
| AADSTS90015 | QueryStringTooLong - řetězec dotazu je příliš dlouhý. |
| AADSTS90016 | MissingRequiredClaim - přístupový token není platný. Požadovaná reklamace chybí. |
| AADSTS90019 | MissingTenantRealm – Azure AD nelze určit identifikátor klienta z požadavku. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - Formát hlavního názvu není platný nebo `name[/host][@realm]` nesplňuje očekávaný formát. Hlavní název je povinný, hostitel a sféra jsou volitelné a mohou být nastaveny na hodnotu null. |
| AADSTS90023 | InvalidRequest - Požadavek ověřovací služby není platný. |
| AADSTS9002313 | InvalidRequest - Požadavek je poškozený nebo neplatný. - Problém je, protože tam bylo něco v nepořádku s žádostí o určitý koncový bod. Návrh na tento problém je získat houslista stopu chyby dochází a chtějí zjistit, zda je žádost skutečně správně formátován, nebo ne. |
| AADSTS90024 | RequestBudgetExceededError - Došlo k přechodné chybě. Zkuste to znovu. |
| AADSTS90033 | MsodsServiceUnavailable - Adresářová služba Microsoft Online (MSODS) není k dispozici. |
| AADSTS90036 | MsodsServiceUnretryableFailure - Došlo k neočekávané, neopakovatelné chybě ze služby WCF hostované službou MSODS. [Otevřete lístek podpory,](../fundamentals/active-directory-troubleshooting-support-howto.md) abyste získali další podrobnosti o chybě. |
| AADSTS90038 | NationalCloudTenantRedirection - zadaný klient 'Y' patří do národního cloudu 'X'. Aktuální instance cloudu 'Z' nefederate s X. Je vrácena chyba přesměrování cloudu. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - Funkce je zakázána. |
| AADSTS90051 | InvalidNationalCloudId – identifikátor národního cloudu obsahuje neplatný identifikátor cloudu. |
| AADSTS90055 | TenantThrottlingError - Existuje příliš mnoho příchozích požadavků. Tato výjimka je vyvolána pro blokované klienty. |
| AADSTS90056 | BadResourceRequest - Chcete-li uplatnit kód pro přístupový token, `/token` aplikace by měla odeslat požadavek POST do koncového bodu. Také před tímto, měli byste zadat autorizační kód a `/token` odeslat jej v požadavku POST do koncového bodu. Přehled toku autorizačního kódu OAuth 2.0 [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)naleznete v tomto článku: . Nasměrujte uživatele `/authorize` do koncového bodu, který vrátí authorization_code. Odesláním požadavku do `/token` koncového bodu uživatel získá přístupový token. Přihlaste se k portálu Azure a zkontrolujte **registrace aplikací > koncových bodů,** abyste potvrdili, že dva koncové body byly správně nakonfigurované. |
| AADSTS90072 | PassThroughUserMfaError - externí účet, který uživatel přihlásí s neexistuje v tenantovi, ke kterému se přihlásili; takže uživatel nemůže splňovat požadavky MFA pro klienta. Účet musí být přidán jako externí uživatel v tenantovi jako první. Odhlaste se a přihlaste se pomocí jiného uživatelského účtu Azure AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - Při pokusu o zpracování zprávy WS-Federation došlo k chybě. Zpráva není platná. |
| AADSTS90082 | OrgIdWsFederationNotSupported - Vybrané zásady ověřování pro požadavek nejsou aktuálně podporovány. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - Účty hosta nejsou pro tento web povoleny. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - Služba nemůže vydat token, protože objekt společnosti ještě nebyl zřízen. |
| AADSTS90086 | OrgIdWsTrustDaTokenVypršela platnost - platnost tokenu DA uživatele vypršela. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - Při vytváření zprávy WS-Federation z identifikátoru URI došlo k chybě. |
| AADSTS90090 | GraphRetryableError - Služba je dočasně nedostupná. |
| AADSTS90091 | GraphService Nedostupný |
| AADSTS90092 | Chyba GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Graph vrácena se zakázaným kódem chyby pro požadavek. |
| AADSTS90094 | AdminConsentRequired - Je vyžadován souhlas správce. |
| AADSTS900382 | Důvěrný klient není v žádosti cross cloudu podporován. |
| AADSTS90100 | InvalidRequestParameter - Parametr je prázdný nebo není platný. |
| AADSTS901002 | AADSTS901002: Parametr požadavku prostředku není podporován. |
| AADSTS90101 | InvalidEmailAddress - Zadaná data nejsou platnou e-mailovou adresou. E-mailová adresa musí `someone@example.com`být ve formátu . |
| AADSTS90102 | InvalidUriParameter - Hodnota musí být platný absolutní identifikátor URI. |
| AADSTS90107 | InvalidXml - Požadavek není platný. Ujistěte se, že data nemají neplatné znaky.|
| AADSTS90114 | InvalidExpiryDate - časové razítko vypršení platnosti hromadného tokenu způsobí, že bude vydán token s ukončenou platností. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - Uživatelský kód je null nebo prázdný.|
| AADSTS90120 | InvalidDeviceFlowRequest - Požadavek byl již autorizován nebo odmítnut. |
| AADSTS90121 | InvalidEmptyRequest - Neplatný prázdný požadavek.|
| AADSTS90123 | IdentityProviderAccessDenied - Token nelze vydat, protože identity nebo deklarace vystavování poskytovatele odmítl požadavek. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - prostředek není podporován `/common` `/consumers` přes koncové body nebo. Místo `/organizations` toho použijte koncový bod specifický pro klienta. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - Uživatel není v systému. Zkontrolujte, zda jste uživatelské jméno zadali správně. |
| AADSTS90126 | DebugPodenrollTenantNotInferred - Typ uživatele není podporován v tomto koncovém bodě. Systém nelze odvodit klienta uživatele z uživatelského jména. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - Aplikace není `/common` podporována `/consumers` přes nebo koncové body. Místo `/organizations` toho použijte koncový bod specifický pro klienta. |
| AADSTS120000 | Změna heslaIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordweak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsNázev_neplatné |
| AADSTS120004 | PasswordChangeOnPremSložitost |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - Došlo k neopakovatelné chybě.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordchangeonPremisesConnectivityFailure PasswordChangeOnPremises |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutNeboZakázáno |
| AADSTS120015 | PasswordchangeadAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | Chyba změny hesla |
| AADSTS120021 | Chyba InternalServiceError společnosti PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - Zaregistrovaný objekt uživatele nemá nakonfigurovaný klíč ID NGC. |
| AADSTS130005 | NgcInvalidSignature - podpis klíče NGC se nezdařil.|
| AADSTS130006 | NgcTransportKeyNotFound - klíč přenosu NGC není nakonfigurován na zařízení. |
| AADSTS130007 | NgcDeviceIsDisabled - Zařízení je zakázáno. |
| AADSTS130008 | NgcDeviceIsNotFound - zařízení odkazované klíčem NGC nebylo nalezeno. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - Požadavek nonce není k dispozici. |
| AADSTS140001 | InvalidSessionKey - Klíč relace není platný.|
| AADST165900 | InvalidApiRequest - Neplatný požadavek. |
| AADSTS220450 | NepodporovanýAndroidWebViewVersion - Verze Chrome WebView není podporována. |
| AADSTS220501 | Neplatnýsoubor Silze |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - Prostředek není nakonfigurován tak, aby přijímal tokeny pouze pro zařízení. |
| AADSTS240001 | BulkAADJTokenUnAuthorized – Uživatel není oprávněn zaregistrovat zařízení ve službě Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing - id_token nelze použít `urn:ietf:params:oauth:grant-type:jwt-bearer` jako grant.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - Správce klienta nakonfiguroval zásady zabezpečení, které blokují tento požadavek. Zkontrolujte zásady zabezpečení, které jsou definovány na úrovni klienta a zjistěte, zda váš požadavek splňuje požadavky zásad. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - Aplikace nebyla v adresáři/tenantovi nalezena. K tomu může dojít, pokud aplikace nebyla nainstalována správcem klienta nebo s nikterou souhlasnou byla přijata libovolnýuživatel v tenantovi. Je možné, že jste chybně nakonfigurovali hodnotu identifikátoru aplikace nebo odeslali požadavek na ověření nesprávnému tenantovi. |
| AADSTS700020 | InteractionRequired - Udělení přístupu vyžaduje interakci. |
| AADSTS700022 | InvalidMultipleResourcesScope - zadaný hodnota pro obor vstupního parametru není platná, protože obsahuje více než jeden prostředek. |
| AADSTS700023 | InvalidResourcelessScope - zadaný hodnota pro obor vstupního parametru není platná při požadavku na přístupový token. |
| AADSTS100000 | UserNotBoundError - Rozhraní API vazby vyžaduje, aby uživatel Azure AD také ověřit pomocí externí ho DIsP, které se ještě nestalo. |
| AADSTS1000002 | BindCompleteInterruptError - Vazba byla úspěšně dokončena, ale uživatel musí být informován. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled - Aplikace je zakázána. |
| AADSTS7500529 | Hodnota SAMLId-Guid není platné ID SAML - Azure AD používá tento atribut k naplnění atributu InResponseTo vrácené odpovědi. ID nesmí začínat číslem, takže běžnou strategií je předřadit řetězec jako "id" na řetězcovou reprezentaci identifikátoru GUID. Například id6c1c178c166d486687be4aaf5e482730 je platné ID. |

## <a name="next-steps"></a>Další kroky

* Máte dotaz nebo nemůžete najít, co hledáte? Vytvořte problém s GitHubem nebo si [přečtěte téma Podpora a možnosti nápovědy pro vývojáře,](active-directory-develop-help-support.md) kde se dozvíte o dalších způsobech, jak získat nápovědu a podporu.
