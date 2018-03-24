---
title: 'Azure Active Directory B2C: Seznámení vlastní zásady sady starter | Microsoft Docs'
description: Téma na Azure Active Directory B2C vlastní zásady
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/25/2017
ms.author: davidmu
ms.openlocfilehash: 624a40b1e40db6ceac9c567926b3932449e7bf7e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Seznámení s vlastní zásady Startovní sady Azure AD B2C vlastních zásad

V této části jsou uvedeny všechny základní prvky B2C_1A_base zásad, která se dodává s **Starter Pack** a který je využít k vytváření vlastních zásad pro prostřednictvím dědičnosti třídy *B2C_1A_base_extensions zásad* .

Jako takový ho zejména zaměřen na typy už definované deklarací identity, transformace deklarací, obsahu definice, poskytovatelů deklarací identity s jejich technické profily a cesty uživatele jádra.

> [!IMPORTANT]
> Společnost Microsoft neposkytuje žádné záruky, vyjádřené nebo předpokládané, s ohledem na informacích uvedených níže. V době GA nebo po může kdykoli před časem GA zavedeny změny.

Zásady B2C_1A_base_extensions i svoje vlastní zásady můžete přepsat tyto definice a prodloužit tuto zásadu nadřazené tím, že poskytuje další ty, které jsou podle potřeby.

Základní prvky *B2C_1A_base zásad* jsou typy deklarací identity, transformace deklarací a definic obsahu. Tyto prvky můžete odkazovat ve vlastní zásady, stejně jako v náchylné *B2C_1A_base_extensions zásad*.

## <a name="claims-schemas"></a>Schémata deklarace identity

Tato deklarace identity, schémata je rozdělené do tří částí:

1.  První oddíl, který obsahuje minimální deklarace identity, které jsou požadovány pro uživatele cesty správně fungovat.
2.  Druhý oddíl, který obsahuje seznam deklarací potřebné pro parametrů řetězce dotazu a další speciální parametry mají být předány jiných zprostředkovatelů deklarací identity, zejména login.microsoftonline.com pro ověřování. **Neměňte prosím tyto deklarace**.
3.  A nakonec třetí oddíl, který obsahuje seznam dalších, volitelných deklarace identity, které mohou být shromažďovány z uživatele, uložené v adresáři a odeslány v tokenech během přihlášení. V této části můžete přidat nový typ deklarace identity a shromažďovat od uživatelů nebo odeslat v tokenu.

> [!IMPORTANT]
> Schéma deklarací identity obsahuje omezení na určité deklarace identity, jako jsou uživatelská jména a hesla. Zásady důvěryhodnosti Framework TF zpracovává Azure AD jako ostatní poskytovatele deklarací identity a všechny její omezení jsou modelována v zásadách premium. Zásady je možné upravovat přidat další omezení, nebo použijte jiného poskytovatele deklarací identity pro úložiště přihlašovacích údajů, který bude mít svůj vlastní omezení.

Typy deklarací identity k dispozici jsou uvedeny níže.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Deklarace identity, které jsou požadovány pro cesty uživatele

Následující deklarace identity jsou požadovány pro uživatele cesty ke správné funkci:

| Typ deklarace identity | Popis |
|-------------|-------------|
| *UserId* | Uživatelské jméno |
| *signInName* | Přihlaste se název |
| *tenantId* | Identifikátor klienta (ID) objektu uživatele v Azure AD B2C Premium |
| *objectId* | Identifikátor objektu (ID) objektu uživatele v Azure AD B2C Premium |
| *password* | Heslo |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Zásady pro hesla používají Azure AD B2C Premium k určení síly hesla, vypršení platnosti, atd. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Telefonní číslo uživatele |
| *Verified.strongAuthenticationPhoneNumber* | |
| *E-mailu* | E-mailovou adresu, které je možné kontaktovat uživatele |
| *signInNamesInfo.emailAddress* | E-mailovou adresu, která uživatel může použít pro přihlášení |
| *otherMails* | E-mailové adresy, které lze použít ke kontaktování uživatele |
| *userPrincipalName* | Uživatelské jméno dle záznamu v Azure AD B2C Premium |
| *upnUserName* | Uživatelské jméno pro vytváření hlavní název uživatele |
| *mailNickName* | Uživatelské jméno e-mailu nick uložen v Azure AD B2C Premium |
| *newUser* | |
| *executed-SelfAsserted-Input* | Deklarace identity, která určuje, zda byly shromažďovány atributy od uživatele |
| *executed-PhoneFactor-Input* | Deklarace identity, která určuje, zda nové telefonní číslo bylo odebráno od uživatele |
| *authenticationSource* | Určuje, zda byl uživatel ověřený v sociálních zprostředkovatele Identity, login.microsoftonline.com nebo místní účet |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Deklarace identity, které jsou potřebné pro parametrů řetězce dotazu a další speciální parametry

Následující deklarace identit vyžadují speciální parametrů (včetně některých parametrů řetězce dotazu) předat do jiných zprostředkovatelů deklarací identity:

| Typ deklarace identity | Popis |
|-------------|-------------|
| *nux* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *nca* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *prompt* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *mkt* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *lc* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *grant_type* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *Obor* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *client_id* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *objectIdFromSession* | Zadaný parametr výchozí zprostředkovatel správy relace k označení, že id objektu načtení z relace jednotného přihlašování |
| *isActiveMFASession* | Zadaný parametr správou relace MFA k označení, že uživatel má aktivní relaci vícefaktorového ověřování |

### <a name="additional-optional-claims-that-can-be-collected"></a>Další (volitelné) deklarace identity, které se můžou shromažďovat

Následující deklarace identity jsou další deklarace identity, které můžete shromažďovat od uživatelů, uložené v adresáři a odesílají v tokenu. Jak je uvedeno před, další deklarace identity lze přidat do tohoto seznamu.

| Typ deklarace identity | Popis |
|-------------|-------------|
| *givenName* | Křestní jméno uživatele (také označované jako křestní jméno) |
| *surname* | Přezdívka uživatele (také označované jako název rodiny nebo příjmení) |
| *Extension_picture* | Obrázek uživatele ze sociálních |

## <a name="claim-transformations"></a>Transformace deklarací identity

Transformace deklarace identity k dispozici jsou uvedeny níže.

| Transformace deklarací identity | Popis |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definice obsahu

Tato část popisuje obsahu definice již byl deklarován v *B2C_1A_base* zásad. Jsou náchylné k odkazuje, přepsat nebo rozšířené podle potřeby v svoje vlastní zásady, stejně jako v těchto obsahu definice *B2C_1A_base_extensions* zásad.

| Poskytovatele deklarací identity | Popis |
|-----------------|-------------|
| *Facebook* | |
| *Místní účet přihlášení* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Vlastní prohlašovanou* | |
| *Místní účet* | |
| *Správa relací* | |
| *Modul zásad Trustframework* | |
| *TechnicalProfiles* | |
| *Vydavatel tokenu* | |

## <a name="technical-profiles"></a>Technické profily

Znázorňuje technické profily už deklarovaný za poskytovatele deklarací identity v této části *B2C_1A_base* zásad. Tyto technické profily jsou náchylné k se dál odkazovat, přepsat nebo rozšířené podle potřeby v svoje vlastní zásady, stejně jako v *B2C_1A_base_extensions* zásad.

### <a name="technical-profiles-for-facebook"></a>Technické profily pro Facebook

| Technické profilu | Popis |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Technické profily pro místní přihlášení účtu

| Technické profilu | Popis |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Technické profily pro Phone Factor

| Technické profilu | Popis |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Technické profily pro Azure Active Directory

| Technické profilu | Popis |
|-------------------|-------------|
| *Běžné AAD* | Technické profil podle jiných AAD xxx technické profilů |
| *AAD-UserWriteUsingAlternativeSecurityId* | Technické profil pro sociálních přihlášení |
| *AAD-UserReadUsingAlternativeSecurityId* | Technické profil pro sociálních přihlášení |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Technické profil pro sociálních přihlášení |
| *AAD-UserWritePasswordUsingLogonEmail* | Technické profil pro místní účty |
| *AAD-UserReadUsingEmailAddress* | Technické profil pro místní účty |
| *AAD-UserWriteProfileUsingObjectId* | Aktualizace záznamu uživatele pomocí objectId technické profilu |
| *AAD-UserWritePhoneNumberUsingObjectId* | Aktualizace záznamu uživatele pomocí objectId technické profilu |
| *AAD-UserWritePasswordUsingObjectId* | Aktualizace záznamu uživatele pomocí objectId technické profilu |
| *AAD-UserReadUsingObjectId* | Technické profil slouží k načtení dat po ověření uživatele |

### <a name="technical-profiles-for-self-asserted"></a>Technické profily pro samoobslužné prohlašovanou

| Technické profilu | Popis |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Technické profily pro místní účet

| Technické profilu | Popis |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Technické profilů pro správu relací

| Technické profilu | Popis |
|-------------------|-------------|
| *SM-nedojde k žádné akci* | |
| *SM-AAD* | |
| *SM SocialSignup* | Název profilu se používá k rozlišení AAD relace mezi přihlašovací nahoru a přihlášení |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Technické profily pro TechnicalProfiles modul zásad Trustframework

V současné době jsou definovány žádné technické profily pro **TechnicalProfiles modul zásad Trustframework** poskytovatele deklarací identity.

### <a name="technical-profiles-for-token-issuer"></a>Technické profily pro vydavatel tokenu

| Technické profilu | Popis |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Uživatel cesty

Znázorňuje cesty uživatel již byl deklarován v této části *B2C_1A_base* zásad. Tyto cesty uživatele jsou náchylné k se dál odkazovat, přepsat nebo rozšířené podle potřeby v svoje vlastní zásady, stejně jako v *B2C_1A_base_extensions* zásad.

| Uživatel cesty | Popis |
|--------------|-------------|
| *Registrace* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
