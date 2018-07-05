---
title: Principy vlastní zásady Spouštěče aktualizací Service pack v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma na vlastní zásady pro Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ebcd7a677acde12558b0f566bce9172a0d00233b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442470"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Principy vlastní zásady úvodní balíček Azure AD B2C vlastních zásad

Tato část obsahuje seznam všech základní prvky B2C_1A_base zásad, který je součástí **Starter Pack** a, který se používá k vytváření vlastních zásad prostřednictvím dědičnosti *B2C_1A_base_extensions zásad* .

V důsledku toho je podrobněji se zaměřuje na typy deklarací identity již definovaných, transformace deklarací, definic obsahu, zprostředkovatelů deklarací identity s jejich technické profily a cesty uživatele core.

> [!IMPORTANT]
> Společnost Microsoft neposkytuje žádné záruky, vyjádřené nebo předpokládané, s ohledem na informace uvedené níže. Změny mohou být zaveden kdykoli před období všeobecné dostupnosti v období všeobecné dostupnosti nebo po.

Vlastní zásady a zásady B2C_1A_base_extensions můžete přepsat tyto definice a rozšířit tuto zásadu nadřazené tím, že poskytuje další značky, podle potřeby.

Jádrem *B2C_1A_base zásady* jsou typy deklarací identity, transformace deklarací a definic obsahu. Tyto prvky můžete odkazovat ve vlastním zásady stejně jako v napadnutelné přes bezpečnostní *B2C_1A_base_extensions zásady*.

## <a name="claims-schemas"></a>Schémata deklarací identity

Tato deklarace identity schémata je rozdělen na tři části:

1.  První oddíl, který obsahuje minimální deklarace identity, které jsou požadovány pro cesty uživatele fungovala správně.
2.  Druhý oddíl, který obsahuje seznam deklarací vyžaduje se pro parametry řetězce dotazu a další zvláštní parametry se mají předat jiných zprostředkovatelů deklarací identity, zejména login.microsoftonline.com pro ověřování. **Neprovádějte žádné změny tyto deklarace**.
3.  A nakonec třetí oddíl, který obsahuje seznam dalších, volitelných deklarací, které můžete shromažďovat od uživatelů, uložené v adresáři a odeslaných v tokenech během přihlášení. V této části můžete přidat nový typ deklarace identity k shromážděných od uživatele a/nebo odeslání v tokenu.

> [!IMPORTANT]
> Schéma deklarací identity obsahuje omezení určitých deklarací identity, jako jsou hesla a uživatelských jmen. Zásady důvěryhodnosti Framework (TF) zpracovává Azure AD jako jakýkoli jiný poskytovatel deklarací identity a jeho omezení jsou modelována ve vlastních zásadách. Chcete-li přidat více omezení, nebo použít jiného zprostředkovatele deklarací identity pro uložení přihlašovacích údajů, které budou mít vlastní omezení je možné upravovat zásady.

Typy deklarací identity k dispozici jsou uvedeny níže.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Deklarace identity, které jsou požadovány pro cesty uživatele

Následující deklarace identity jsou požadovány pro cesty uživatele fungovala správně:

| Typ deklarace identity | Popis |
|-------------|-------------|
| *ID uživatele* | Uživatelské jméno |
| *signInName* | Přihlaste se název |
| *ID Tenanta* | Identifikátor tenanta (ID) objektu uživatele v Azure AD B2C |
| *ID objektu* | Identifikátor objektu (ID) objektu uživatele v Azure AD B2C |
| *Heslo* | Heslo |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Zásady pro hesla pomocí Azure AD B2C používá k určení síly hesla, vypršení platnosti, atd. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Telefonní číslo uživatele |
| *Verified.strongAuthenticationPhoneNumber* | |
| *E-mailu* | Je možné kontaktovat uživatele e-mailovou adresu |
| *signInNamesInfo.emailAddress* | E-mailovou adresu, která uživatel může použít k přihlášení |
| *otherMails* | E-mailové adresy, které lze použít ke kontaktování uživatele |
| *userPrincipalName* | Uživatelské jméno dle záznamu v Azure AD B2C |
| *upnUserName* | Uživatelské jméno pro vytvoření hlavního názvu uživatele |
| *mailNickName* | Název Přezdívka pošty uživatele uložené v Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Deklarace identity, která určuje, zda byly atributů shromážděných od uživatele |
| *executed-PhoneFactor-Input* | Deklarace identity, která určuje, jestli se nové telefonní číslo shromážděných od uživatele |
| *authenticationSource* | Určuje, zda byl uživatel ověřený zprostředkovatele sociální Identity, login.microsoftonline.com nebo místní účet |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Vyžaduje se pro parametry řetězce dotazu a další zvláštní parametry deklarací identity

Následující deklarace identit vyžadují k předání na zvláštní parametry (včetně některých parametrů řetězce dotazu) do jiných zprostředkovatelů deklarací identity:

| Typ deklarace identity | Popis |
|-------------|-------------|
| *Nux* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *nca* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *prompt* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *mkt* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *lc* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *Parametr grant_type* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *Obor* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *client_id* | Speciální parametr předaný pro místní účet ověřování login.microsoftonline.com |
| *objectIdFromSession* | Parametr poskytované poskytovateli výchozí relace správy k označení, že ID objektu byl načten z relace jednotného přihlašování |
| *isActiveMFASession* | Parametr poskytované Správa relací vícefaktorové ověřování k označení, že uživatel má aktivní relace MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Další (volitelné) deklarace identity, které se můžou shromažďovat

Následující deklarace identity jsou další deklarace identity, které můžete shromažďovat od uživatelů, uložené v adresáři a odeslali v tokenu. Jak je uvedeno před další deklarace identity je přidat do tohoto seznamu.

| Typ deklarace identity | Popis |
|-------------|-------------|
| *givenName* | Jméno uživatele (také nazývané křestní jméno) |
| *Příjmení* | Příjmení uživatele (označované také jako název rodiny nebo příjmení) |
| *Extension_picture* | Obrázek uživatele z sociální sítě |

## <a name="claim-transformations"></a>Transformace deklarací identit

Transformace deklarací identity k dispozici jsou uvedeny níže.

| Transformace deklarací identity | Popis |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definice obsahu

Tato část popisuje už deklarované v obsahu definice *B2C_1A_base* zásad. Tyto definice obsahu jsou však náchylné k odkazuje, přepsat nebo podle potřeby v svoje vlastní zásady, stejně jako v rozšířit *B2C_1A_base_extensions* zásad.

| Zprostředkovatele deklarací identity | Popis |
|-----------------|-------------|
| *Facebook* | |
| *Registrace místního účtu* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Vlastní s prohlašovanou* | |
| *Místní účet* | |
| *Správa relací* | |
| *Modul zásad Trustframework* | |
| *TechnicalProfiles* | |
| *Vydavatel tokenu* | |

## <a name="technical-profiles"></a>Technické profily

Tato část popisuje technické profily již byla deklarována na poskytovatele deklarací identity v *B2C_1A_base* zásad. Tyto technické profily jsou však náchylné k být dále odkazovat, přepsání a/nebo rozšířené podle potřeby v svoje vlastní zásady, stejně jako v *B2C_1A_base_extensions* zásad.

### <a name="technical-profiles-for-facebook"></a>Technické profily pro Facebook

| Technický profil | Popis |
|-------------------|-------------|
| *OAUTH pro Facebook* | |

### <a name="technical-profiles-for-local-account-signin"></a>Technické profily pro přihlášení místním účtem

| Technický profil | Popis |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Technické profily pro Phone Factor

| Technický profil | Popis |
|-------------------|-------------|
| *Vstup PhoneFactor* | |
| *PhoneFactor InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Technické profily pro Azure Active Directory

| Technický profil | Popis |
|-------------------|-------------|
| *AAD – společné* | Technický profil součástí jiné AAD-xxx technické profily |
| *AAD-UserWriteUsingAlternativeSecurityId* | Technický profil pro přihlašování přes sociální sítě |
| *AAD-UserReadUsingAlternativeSecurityId* | Technický profil pro přihlašování přes sociální sítě |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Technický profil pro přihlašování přes sociální sítě |
| *AAD-UserWritePasswordUsingLogonEmail* | Technický profil pro místní účty |
| *AAD UserReadUsingEmailAddress* | Technický profil pro místní účty |
| *AAD-UserWriteProfileUsingObjectId* | Technický profil pro aktualizaci záznamu uživatele pomocí ID objektu |
| *AAD-UserWritePhoneNumberUsingObjectId* | Technický profil pro aktualizaci záznamu uživatele pomocí ID objektu |
| *AAD-UserWritePasswordUsingObjectId* | Technický profil pro aktualizaci záznamu uživatele pomocí ID objektu |
| *AAD-UserReadUsingObjectId* | Technický profil slouží k načtení dat po ověření uživatele |

### <a name="technical-profiles-for-self-asserted"></a>Technické profily pro samoobslužné Prosazený

| Technický profil | Popis |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Technické profily pro místní účet

| Technický profil | Popis |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Technické profily pro Správa relací

| Technický profil | Popis |
|-------------------|-------------|
| *SM Noop* | |
| *SM AAD* | |
| *SM SocialSignup* | Název profilu se používá k rozlišení AAD relace mezi sign up a přihlášení |
| *SM-SocialLogin* | |
| *SM MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Technické profily pro modul zásad důvěryhodnosti rozhraní framework

V současné době jsou definovány žádné technické profily pro **TechnicalProfiles modul zásad Trustframework** zprostředkovatele deklarací identity.

### <a name="technical-profiles-for-token-issuer"></a>Technické profily pro vydavatel tokenu

| Technický profil | Popis |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Cesty uživatele

Tato část popisuje cesty uživatele už deklarovaný v *B2C_1A_base* zásad. Tyto cesty uživatele jsou však náchylné k být dále odkazovat, přepsání a/nebo rozšířené podle potřeby v svoje vlastní zásady, stejně jako v *B2C_1A_base_extensions* zásad.

| Cesta uživatele | Popis |
|--------------|-------------|
| *Registrace* | |
| *Přihlášení* | |
| *SignUpOrSignIn* | |
| *Úprava profilu* | |
| *PasswordReset* | |
