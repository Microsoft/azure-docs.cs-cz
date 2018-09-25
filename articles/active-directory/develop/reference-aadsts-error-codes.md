---
title: Azure Active Directory ověřování a autorizace kódy chyb | Dokumentace Microsoftu
description: Další informace o chybových kódech AADSTS, které jsou vráceny z Azure AD službu tokenů zabezpečení (STS).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.openlocfilehash: 0643ae39d24f8ef0c1c1c18dcf73cf840c95e277
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956321"
---
# <a name="authentication-and-authorization-error-codes"></a>Kódy chyb ověřování a autorizace

Hledáte informace o chybových kódech AADSTS, které jsou vráceny od služby tokenů zabezpečení Azure Active Directory (Azure AD) (STS)? Přečtěte si tento dokument se najít AADSTS popisy chyb, opravy a některé navrhovaná alternativní řešení.

> [!NOTE]
> Tyto informace jsou předběžné a můžou se změnit. Máte dotaz nebo nemůže najít, co hledáte? Vytvoření problému Githubu, nebo zobrazit [možnosti podporu a nápovědu pro vývojáře](active-directory-develop-help-support.md) Další informace o jiných způsobech, jakými můžete získat pomoc a podporu.

## <a name="aadsts-error-codes"></a>Kódy chyb AADSTS

| Chyba | Popis |
|---|---|
| AADSTS16000 | SelectUserAccount – to je přerušení vyvolané službou Azure AD, což vede k uživatelské rozhraní, které umožňuje uživateli vybrat z víc platné relace jednotného přihlašování. Tato chyba je velmi běžné a mohou být vráceny do aplikace, pokud `prompt=none` je zadán. |
| AADSTS16001 | UserAccountSelectionInvalid – zobrazí se vám tato chyba Pokud uživatel klikne na dlaždici, která byla zamítnuta logiky vyberte relace. Když se aktivuje, k této chybě umožňuje uživateli obnovit vybere z aktualizovaného seznamu dlaždic/relací, nebo výběrem jiného účtu. Této chybě může dojít z důvodu kód chyb nebo závodu podmínku. |
| AADSTS16002 | AppSessionSelectionInvalid - SID požadavek zadán parametr-app nebyl splněn.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant |
| AADSTS17003 | CredentialKeyProvisioningFailed |
| AADSTS20001 | WsFedSignInResponseError – se vyskytl problém s federovaným zprostředkovatele Identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS20012 | WsFedMessageInvalid – se vyskytl problém s federovaným zprostředkovatele Identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS20033 | FedMetadataInvalidTenantName – se vyskytl problém s federovaným zprostředkovatele Identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40008 | OAuth2IdPUnretryableServerError – se vyskytl problém s federovaným zprostředkovatele Identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError – se vyskytl problém s federovaným zprostředkovatele Identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40010 | OAuth2IdPRetryableServerError – se vyskytl problém s federovaným zprostředkovatele Identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError – se vyskytl problém s federovaným zprostředkovatele Identity. Pokud chcete tento problém vyřešit, obraťte se na svůj distribuční bod vydávání (IDP). |
| AADSTS50000 | TokenIssuanceError – se vyskytl problém s přihlašovací službou. Pokud chcete tento problém vyřešit, [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50001 | InvalidResource - prostředku je zakázán nebo neexistuje. Zkontrolujte kód vaší aplikace k zajištění, zda jste zadali adresu URL přesné prostředků pro prostředek, který se pokoušíte získat přístup.  |
| AADSTS50002 | NotAllowedTenant - přihlášení se nezdařilo z důvodu access s omezeným přístupem proxy v tenantovi. Pokud se jedná vlastní zásady tenanta, můžete změnit nastavení tenanta s omezeným přístupem k vyřešení problému. |
| AADSTS50003 | MissingSigningKey - přihlášení se nezdařilo z důvodu chybějícího podpisový klíč nebo certifikát. To může být, protože nebylo žádný podpisový klíč nakonfigurovaný v aplikaci. Podívejte se na řešení uvedená na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Pokud se stále zobrazuje problémy, obraťte se na vlastníka aplikace nebo aplikace správce. |
| AADSTS50005 | DevicePolicyError – uživatel se pokusil přihlásit se k zařízení z platformu, která není v současnosti podporován pomocí zásad podmíněného přístupu. |
| AADSTS50006 | InvalidSignature – ověření podpisu se nezdařilo z důvodu neplatného podpisu. |
| AADSTS50007 | PartnerEncryptionCertificateMissing – partner šifrovací certifikát nebyl nalezen pro tuto aplikaci. [Vytvořit lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) s Microsoftem získat tyto pevné. |
| AADSTS50008 | InvalidSamlToken - kontrolní výraz SAML je chybějící nebo nesprávně nakonfigurované v tokenu. Obraťte se na svého federačního zprostředkovatele. |
| AADSTS50010 | AudienceUriValidationFailed – identifikátor URI cílové skupiny pro aplikaci pro ověření se nezdařilo, protože nebyly nakonfigurovány žádné cílové skupiny tokenu. |
| AADSTS50011 | InvalidReplyTo - adresa pro odpovědi chybí, je chybně nakonfigurovaný, nebo neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci. Vyzkoušejte řešení uvedené na adrese [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Pokud se stále zobrazuje problémy, obraťte se na vlastníka aplikace nebo aplikace správce. |
| AADSTS50012 | AuthenticationFailed – ověření se nezdařilo pro jednu z následujících důvodů:<ul><li>Název předmětu podpisového certifikátu není autorizovaný.</li><li>V názvu subjektu autorizované nebyl nalezen odpovídající důvěryhodné autority zásad</li><li>Řetěz certifikátů není platný</li><li>Podpisový certifikát je neplatný</li><li>Zásady nejsou nakonfigurované na tenantovi</li><li>Kryptografický otisk podpisového certifikátu není autorizovaný.</li><li>Kontrolní výraz klienta obsahuje neplatný podpis</li></ul> |
| AADSTS50013 | InvalidAssertion - kontrolní výraz je z různých důvodů neplatné – vydavatel tokenu neodpovídá rozhraní api verze v rámci své doby platnosti v rozsahu - vypršela platnost – poškozený - obnovovací token v kontrolního výrazu není primární obnovovací token. |
| AADSTS50014 | GuestUserInPendingState |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState |
| AADSTS50017 | CertificateValidationFailed - certifikační ověření se nezdařilo, důvody z následujících důvodů:<ul><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Nepodařilo se najít očekávaný CrlSegment.</li><li>Na seznamu důvěryhodných certifikátů nebylo možné najít certifikát vystavitele.</li><li>Distribuční bod rozdílového seznamu CRL je nakonfigurovaný bez odpovídajícího distribučního bodu CRL.</li><li>Z důvodu vypršení časového limitu se nepodařilo načíst platné segmenty CRL.</li><li>CRL nejde stáhnout.</li></ul>Obraťte se na správce tenanta. |
| AADSTS50020 | UserUnauthorized - uživateli chybí oprávnění k volání tohoto koncového bodu. |
| AADSTS50027 | InvalidJwtToken – neplatný token JWT token z následujících důvodů:<ul><li>Neobsahuje deklaraci identity hodnoty nonce nebo subjektu.</li><li>Identifikátor subjektu se neshoduje.</li><li>Deklarace identity idTokenu má duplicitní deklaraci identity.</li><li>Neočekávaný vystavitel</li><li>Neočekávaná cílová skupina</li><li>Nenachází se v platném časovém rozsahu. </li><li>Nesprávný formát tokenu</li><li>U externího ID tokenu od vystavitele se nezdařilo ověření podpisu.</li></ul> |
| AADSTS50029 | Neplatný identifikátor URI – název domény obsahuje neplatné znaky. Obraťte se na správce tenanta. |
| AADSTS50032 | WeakRsaKey – označuje chybné uživatelské pokusem o použití slabý klíč RSA. |
| AADSTS50033 | RetryableError – označuje přechodná chyba nesouvisí databázových operací. |
| AADSTS50034 | UserAccountNotFound - pro přihlášení do této aplikace, musí být tento účet přidaný do adresáře. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt – hodnota salt vyžadovaných ke generování pairwise identifikátor nebyl nalezen v objektu zabezpečení. Obraťte se na správce tenanta. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | Deklarace identity subjektu neshody vystavitele SubjectMismatchesIssuer - v kontrolní výraz klienta. Obraťte se na správce tenanta. |
| AADSTS50049 | NoSuchInstanceForDiscovery – neznámá nebo neplatná instance. |
| AADSTS50050 | MalformedDiscoveryRequest – má chybný formát. |
| AADSTS50053 | IdsLocked – účet je uzamčený, protože uživatel se pokusil přihlásit příliš mnohokrát pomocí nesprávného ID uživatele nebo hesla. |
| AADSTS50055 | InvalidPasswordExpiredPassword – hesla vypršela. |
| AADSTS50056 | Neplatná nebo null hesel – hesla neexistuje v úložišti pro tohoto uživatele. |
| AADSTS50057 | UserDisabled – uživatelský účet je zakázaný. Správce tento účet zakázal. |
| AADSTS50058 | UserInformationNotProvided – to znamená, že uživatel není přihlášený. To je velmi běžné chyby, která očekává se, když uživatel není ověřena a ještě nepřihlásil.</br>Pokud tato chyba je podporována v kontextu jednotného přihlašování. Pokud uživatel už jenom přihlašoval, to znamená, že relace jednotného přihlašování nebyl nalezen nebo je neplatný.</br>Tuto chybu mohou být vráceny do aplikace, pokud příkazový řádek = není zadaný žádný. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - Tenanta identifikační informace se nenašel v jednom požadavku nebo odvozené od všech zadaných přihlašovacích údajů. Uživatel může kontaktovat správce tenanta, který vám pomůže vyřešit problém. |
| AADSTS50061 | SignoutInvalidRequest – odhlášení požadavek je neplatný. |
| AADSTS50064 | CredentialAuthenticationError |
| AADSTS50068 | SignoutInitiatorNotParticipant |
| AADSTS50070 | SignoutUnknownSessionIdentifier |
| AADSTS50071 | SignoutMessageExpired |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - uživatel potřebuje zaregistrovat k druhému faktoru ověřování (interaktivní). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - je požadováno silné ověřování a uživatel neprošel ověřovacím testem MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - uživatel musí používat vícefaktorové ověřování pro přístup k tomuto prostředku. Zkuste to znovu s novou požadavku authorize pro prostředek. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired – z důvodu změny konfigurace provedené správcem, nebo protože uživatel přesune do nového umístění, musí uživatel zadat použití služby Multi-Factor authentication. |
| AADSTS50085 | Obnovovací token vyžaduje přihlášení prostřednictvím distribučního bodu vydávání (IDP) sociální sítě. Požádejte uživatele, aby se zkusil pomocí uživatelského jména a hesla přihlásit znovu. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError |
| AADSTS50089 | Platnost tokenu toku vypršela – ověření se nezdařilo. Požádejte uživatele, aby se zkusil pomocí uživatelského jména a hesla přihlásit znovu. |
| AADSTS50097 | DeviceAuthenticationRequired - je požadováno ověřování zařízení. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - podpis JWT není platný. |
| AADSTS50105 | EntitlementGrantsNotFound – přihlášený uživatel není přiřazený k roli pro podepsanou v aplikaci. Přiřadíte uživatele k aplikaci. Další informace:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri – objekt sféry požadovaný federace neexistuje. Obraťte se na správce tenanta. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat – problém s hlavičkou tokenů JWT. Obraťte se na správce tenanta. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - transformaci deklarací identity obsahuje neplatný vstupní parametr. Obraťte se na správce klienta, aby aktualizovat zásady. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - Sign in bylo přerušeno z důvodu obnovení hesla nebo položka registrace hesla. |
| AADSTS50126 | InvalidUserNameOrPassword - Chyba při ověřování přihlašovacích údajů z důvodu neplatného uživatelského jména nebo hesla. |
| AADSTS50127 | BrokerAppNotInstalled - uživatel potřebuje k instalaci zprostředkující aplikace, abyste získali přístup k tomuto obsahu. |
| AADSTS50128 | Neplatný název domény – identifikující informace o tenantovi se nenachází ani v požadavku a ani je není možné odvodit z poskytnutých přihlašovacích údajů.|
| AADSTS50129 | DeviceIsNotWorkplaceJoined – připojení k pracovní ploše se vyžaduje k registraci zařízení. |
| AADSTS50131 | ConditionalAccessFailed – označuje různých chybách podmíněného přístupu, jako je například špatný stav zařízení Windows, žádost se zablokovala, protože podezřelou aktivitu, zásady přístupu nebo rozhodnutí zásad zabezpečení. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - relace není platná z důvodu vypršení platnosti hesla nebo nedávné změny hesla. |
| AADSTS50133 | SsoArtifactRevoked - relace není platná z důvodu vypršení platnosti hesla nebo nedávné změny hesla. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter |
| AADSTS50135 | PasswordChangeCompromisedPassword – Změna hesla se vyžaduje kvůli účet rizika. |
| AADSTS50136 | RedirectMsaSessionToApp – jeden MSA relace zjištěna. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken |
| AADSTS50140 | KmsiInterrupt - tento došlo k chybě z důvodu přerušení "Neodhlašovat" při přihlášení uživatele. Více informací získáte, když [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
| AADSTS50143 | Neshoda relace – relace je neplatná, protože se tenant uživatele kvůli jinému prostředku neshoduje s nápovědou domény. Více informací získáte, když [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md) a odešlete ID korelace, ID požadavku a kód chyby. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword – vypršela platnost hesla uživatele služby Active Directory. Generovat nové heslo pro uživatele nebo uživatele, použijte nástroj pro samoobslužné resetování k resetování hesla. |
| AADSTS50146 | MissingCustomSigningKey – tato aplikace je potřeba nakonfigurovat pomocí podpisového klíče specifický pro aplikace. Buď není pomocí tohoto klíče nakonfigurovaná nebo klíči vypršela platnost nebo ještě klíč v platnost nevstoupil. |
| AADSTS50147 | MissingCodeChallenge |
| AADSTS50155 | DeviceAuthenticationFailed – ověřování pomocí zařízení pro tohoto uživatele se nezdařilo. |
| AADSTS50158 | ExternalSecurityChallenge - externí bezpečnostní kontroly nebyla splněná. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration – odešle externím poskytovatelem je dostatečně nebo deklarace identity nebyl nalezen požadovaný k externímu poskytovateli. |
| AADSTS50166 | ExternalClaimsProviderThrottled – nepovedlo se odeslat požadavek na zprostředkovatele deklarací identity. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired – klient je schopni získat token jednotného přihlašování přes rozšíření účty systému Windows 10, ale nebyl v žádosti se našel token nebo platnost zadaného tokenu vypršela. |
| AADSTS50169 | InvalidRequestBadRealm - sféru není nakonfigurované sféru aktuální obor názvů služby. |
| AADSTS50170 | MissingExternalClaimsProviderMapping |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - externí výzvy se nepodporuje pro průchozí uživatele. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers – řízení relace není podporována pro průchozí uživatele. |
| AADSTS50180 | Je potřeba WindowsIntegratedAuthMissing – ověření integrované Windows. Povolte tenantovi bezproblémové jednotné přihlašování. |
| AADSTS50187 | DeviceInformationNotProvided |
| AADSTS51000 | RequiredFeatureNotEnabled |
| AADSTS51001 | DomainHintMustbePresent – Nápověda domény musí být k dispozici s identifikátorem zabezpečení v místním nebo hlavní název uživatele v místním. |
| AADSTS51004 | Uživatelský účet UserAccountNotInDirectory - neexistuje v adresáři. |
| AADSTS51005 | TemporaryRedirect - ekvivalent stav protokolu HTTP 307, což znamená, že se nachází na zadaný identifikátor URI v hlavičce umístění požadované informace. Když dostanete tento stav, postupujte podle hlavičku location, který je přidružený k odpovědi. Při původní metodu požadavku POST, přesměrovaný požadavek také použít metodu POST. |
| AADSTS51006 | Je potřeba ForceReauthDueToInsufficientAuth – ověření integrované Windows. Uživatel přihlášen pomocí tokenu relace, které chybí deklarace identity ověření integrované Windows. Požádat o uživateli se přihlásit znovu. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - uživatel neposkytl souhlasu pro přístup k prostředkům LinkedIn. |
| AADSTS53000 | DeviceNotCompliant – zásady podmíněného přístupu vyžaduje vyhovující zařízení a zařízení nedodržuje předpisy. Uživatel musí zaregistrovat svoje zařízení s poskytovatelem služby schválené MDM, jako je Intune. |
| AADSTS53001 | DeviceNotDomainJoined – zásady podmíněného přístupu vyžaduje zařízení připojené k doméně a zařízení není připojené k doméně. Použití uživatele domény se připojili k zařízení. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp – aplikaci, není schválených aplikací pro podmíněný přístup. Uživatel potřebuje používat jeden z aplikace v seznamu schválených aplikací má použít k získání přístupu. |
| AADSTS53003 | Zásady podmíněného přístupu nezablokoval BlockedByConditionalAccess - přístup. Zásady přístupu neumožňuje vystavování tokenů. |
| AADSTS53004 | ProofUpBlockedDueToRisk – uživatel je potřeba dokončit proces registrace služby Multi-Factor authentication před přístupem k tomuto obsahu. Uživatel by se měl zaregistrovat k vícefaktorovému ověřování. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist – uživatel nebo správce nevyjádřil souhlas s použitím aplikace s ID X. odešlete žádost o interaktivní autorizaci pro tohoto uživatele a prostředek. |
| AADSTS65004 | UserDeclinedConsent - uživatel odmítl souhlas s přístupem k aplikaci. Požádejte uživatele, aby se zkusil znovu přihlásit a udělil aplikaci souhlas.|
| AADSTS65005 | MisconfiguredApplication - aplikace vyžaduje přístup k seznamu prostředků neobsahuje aplikace zjistitelné prostředkem nebo klientská aplikace požadovala přístup k prostředku, který nebyl zadán v přístupovém seznamu požadovaných prostředků nebo služba Graph vrátila chybný žádost nebo prostředek se nenašel. Pokud aplikace podporuje SAML, jste nakonfigurovali aplikaci s identifikátorem nesprávné (entita). Vyzkoušejte řešení uvedené pro SAML v následujícím odkazu: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant – ověření se nezdařilo. Obnovovací token není platný. Chyba může být z následujících důvodů:<ul><li>Token vazby záhlaví je prázdný</li><li>Token vazby hash neodpovídá</li></ul> |
| AADSTS70001 | UnauthorizedClient – aplikace je zakázaná. |
| AADSTS70002 | InvalidClient - Chyba při ověřování přihlašovacích údajů. Zadaná hodnota client_secret neodpovídá očekávané hodnotě pro tohoto klienta. Opravte hodnotu client_secret a zkuste to znovu. Další informace najdete v tématu [můžete požádat o přístupový token autorizační kód](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType – aplikace vrátil typ, který nepodporované udělení. |
| AADSTS70004 | InvalidRedirectUri – aplikace vrátila neplatný identifikátor URI přesměrování. Adresa přesměrování specifikovaná klientem neodpovídá žádné nakonfigurované adrese ani žádné adrese na seznamu schválených adres OIDC. |
| AADSTS70005 | UnsupportedResponseType – aplikace vrátil typ, který Nepodporovaná odpověď z následujících důvodů:<ul><li>Typ odpovědi "token" není povolena pro aplikaci</li><li>Typ odpovědi „id_token“ vyžaduje rozsah „OpenID“ – v parametru wctx obsahuje nepodporovanou hodnotu parametru OAuth.</li></ul> |
| AADSTS70007 | UnsupportedResponseMode – aplikace vrátil nepodporovanou hodnotu `response_mode` při vyžádání tokenu.  |
| AADSTS70008 | ExpiredOrRevokedGrant - obnovovacího tokenu vypršela platnost kvůli nečinnosti. Token, který byl vydán na XXX a byla pro určitou dobu neaktivní. |
| AADSTS70011 | InvalidScope - rozsahem požadovaným aplikace je neplatný. |
| AADSTS70012 | MsaServerError - při ověřování uživatele MSA (příjemce) došlo k chybě serveru. Zkuste to prosím znovu. Pokud k chybě dochází opakovaně, [otevřete požadavek na podporu](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS70016 | AuthorizationPending – chyba tok OAuth 2.0 zařízení. Ověření čeká na vyřízení. Zařízení se pokusí navázat požadavku dotazování. |
| AADSTS70018 | BadVerificationCode – neplatný ověřovací kód z důvodu uživatele zadáte nesprávné uživatelského kódu pro tok kódu zařízení. Autorizace není schválený. |
| AADSTS70019 | Ověřovací kód CodeExpired – platnost vypršela. Požádejte uživatele, zkuste přihlásit. |
| AADSTS75001 | BindingSerializationError - došlo k chybě při vytváření vazby zprávu SAML. |
| AADSTS75003 | UnsupportedBindingError – aplikace vrátil chybu související s nepodporované vazby (odpovědí protokolu SAML nelze odeslat prostřednictvím vazby než HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid – Azure AD nepodporuje požadavek SAML odeslané aplikací pro jednotné přihlašování. |
| AADSTS75008 | RequestDeniedError - žádost z aplikace byl odepřen, protože žádost SAML měla neočekávaný cílový. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - metodu ověřování, podle kterého je uživatel ověřený službou neodpovídá požadované metody ověřování. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - žádostí o ověření typu SAML2 má neplatný NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - ověřovací Agent se nemůže připojit ke službě Active Directory. Ujistěte se, že agent servery jsou členy stejné doménové struktuře AD jako uživatelé, jejichž hesla je nutné ověřit a je bylo možné se připojit ke službě Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout – žádost o ověření hesla vypršení časového limitu. Ujistěte se, že služba Active Directory je dostupná a odpovídá na požadavky od agentů. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - při zpracování odpovědi z ověřování agenta došlo k neznámé chybě. Zkuste požadavek. Pokud bude nadále selhávat, [vytvořit lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md) zobrazíte další podrobnosti o chybě. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - ověřovací Agent není schopen ověřit heslo uživatele. V protokolech agenta pro další informace a ověřte, že služby Active Directory funguje podle očekávání. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - ověřovací Agent není schopen dešifrovat heslo. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours – uživatelé se pokusily o připojení mimo povolených hodin (to je určeno ve službě AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew – pokusy o ověření nebylo možné dokončit z důvodu čas nerovnoměrné rozdělení mezi počítači spuštěn authentication agent a AD. Opravte čas synchronizace problémy. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated – pokusy o ověření protokolem Kerberos se nezdařilo. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported – ověřování balíčku se nepodporuje. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - nebyla nalezena žádná hodnota hlavičky autorizace. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - tenant není povolená pro bezproblémové jednotné přihlašování. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat – nepovedlo se ověřit lístek Kerberos uživatele. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - bezproblémové jednotné přihlašování selhalo, protože vypršela platnost lístku Kerberos uživatele nebo je neplatný. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed – nepovedlo se najít objekt uživatele na základě informací v lístku Kerberos uživatele. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - uživatel pokoušel se přihlásit ke službě Azure AD se liší od uživatele přihlášené k zařízení. |
| AADSTS90002 | InvalidTenantName – název tenanta nebyl nalezen v úložišti. Ujistěte se, že máte ID klienta správné. |
| AADSTS90004 | InvalidRequestFormat |
| AADSTS90005 | InvalidRequestWithMultipleRequirements |
| AADSTS90006 | ExternalServerRetryableError |
| AADSTS90007 | InvalidSessionId |
| AADSTS90008 | TokenForItselfRequiresGraphPermission |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier |
| AADSTS90010 | NotSupported |
| AADSTS90012 | RequestTimeout |
| AADSTS90013 | InvalidUserInput |
| AADSTS90014 | MissingRequiredField - tomto kódu chyby můžou objevit v různých případech při očekávané pole se nenachází v přihlašovacích údajích. |
| AADSTS90015 | QueryStringTooLong |
| AADSTS90016 | MissingRequiredClaim |
| AADSTS90019 | MissingTenantRealm – Azure AD se nepodařilo určit identifikátor tenanta z požadavku. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat – hlavní název formátu je neplatný nebo neodpovídá očekávané `name[/host][@realm]` formátu. Hlavní název je povinný, hostitele a sféry jsou volitelné a může být nastavená na hodnotu null. |
| AADSTS90023 | InvalidRequest |
| AADSTS90024 | RequestBudgetExceededError |
| AADSTS90033 | MsodsServiceUnavailable |
| AADSTS90036 | MsodsServiceUnretryableFailure |
| AADSTS90038 | NationalCloudTenantRedirection - zadaného klienta "Y" patří do národních Cloudů "X". Aktuální cloudu instanci "Z" není federaci s X. Vrátí se chyba přesměrování cloudu. |
| AADSTS90043 | NationalCloudAuthCodeRedirection |
| AADSTS90051 | InvalidNationalCloudId |
| AADSTS90055 | TenantThrottlingError – existuje příliš mnoho příchozích požadavků. Tato výjimka je vyvolána pro blokované klienty. |
| AADSTS90056 | BadResourceRequest - chcete uplatnit kód pro přístupový token, aplikace by měli poslat požadavkům POST odeslaných `/token` koncového bodu. Také, před tím, měli byste poskytují autorizační kód a odeslat v požadavku POST k `/token` koncového bodu. Přečtěte si tento článek Přehled tok autorizačního kódu OAuth 2.0: [ https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Nejprve musíte pokyn uživateli `/authorize` koncový bod, který vrátí authorization_code. Tím, že publikuje požadavek `/token` koncový bod, uživatel získá přístupový token. Přihlaste se na webu Azure portal a zkontrolujte **registrace aplikací > Koncové body** potvrďte, že dva koncové body byly nakonfigurované správně. |
| AADSTS90072 | PassThroughUserMfaError |
| AADSTS90081 | OrgIdWsFederationMessageInvalid |
| AADSTS90082 | OrgIdWsFederationNotSupported |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed |
| AADSTS90090 | GraphRetryableError |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Graph vrátila kód chyby zakázáno pro daný požadavek. |
| AADSTS90094 | AdminConsentRequired |
| AADSTS90100 | InvalidRequestParameter |
| AADSTS90101 | InvalidEmailAddress |
| AADSTS90102 | InvalidUriParameter |
| AADSTS90107 | InvalidXml |
| AADSTS90114 | InvalidExpiryDate |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode |
| AADSTS90120 | InvalidDeviceFlowRequest |
| AADSTS90121 | InvalidEmptyRequest |
| AADSTS90123 | IdentityProviderAccessDenied |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported |
| AADSTS90125 | DebugModeEnrollTenantNotFound |
| AADSTS90126 | DebugModeEnrollTenantNotInferred |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated |
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound |
| AADSTS130005 | NgcInvalidSignature |
| AADSTS130006 | NgcTransportKeyNotFound |
| AADSTS130007 | NgcDeviceIsDisabled |
| AADSTS130008 | NgcDeviceIsNotFound |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce |
| AADSTS140001 | InvalidSessionKey |
| AADSTS165000 | InvalidRequestCanary |
| AADSTS165004 | InvalidApiCanary |
| AADSTS165900 | InvalidApiRequest |
| AADSTS220450 | UnsupportedAndroidWebViewVersion |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - zdroj není nakonfigurovaný tak, aby přijímal pouze zařízení tokeny. |
| AADSTS240001 | BulkAADJTokenUnauthorized |
| AADSTS240002 | RequiredClaimIsMissing |
| AADSTS700020 | InteractionRequired |
| AADSTS700022 | InvalidMultipleResroucesScope |
| AADSTS700023 | InvalidResourcelessScope |
| AADSTS900000 | WebWatsonEnvironmentError |
| AADSTS1000000 | UserNotBoundError |
| AADSTS1000002 | BindCompleteInterruptError |

## <a name="next-steps"></a>Další postup

* Máte dotaz nebo nemůže najít, co hledáte? Vytvoření problému Githubu, nebo zobrazit [možnosti podporu a nápovědu pro vývojáře](active-directory-develop-help-support.md) Další informace o jiných způsobech, jakými můžete získat pomoc a podporu.