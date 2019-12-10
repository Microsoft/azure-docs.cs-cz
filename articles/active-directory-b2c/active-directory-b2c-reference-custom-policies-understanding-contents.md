---
title: Součásti úvodní sady vlastních zásad
titleSuffix: Azure AD B2C
description: Přehled zásad v úvodní sadě vlastních zásad Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7d49bd9af80b1bb9bd86466269b14ba0a47181e0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948177"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Principy vlastních zásad pro úvodní sadu vlastních zásad Azure AD B2C

Tato část obsahuje seznam všech základních prvků B2C_1A_base zásad, které se dodávají s **úvodní** sadou a které se využívají k vytváření vlastních zásad prostřednictvím dědičnosti *zásad B2C_1A_base_extensions*.

V takovém případě se podrobněji zaměřuje na už definované typy deklarací identity, transformace deklarací, definice obsahu, zprostředkovatele deklarací identity s jejich technickými profily a základní cesty uživatelů.

> [!IMPORTANT]
> Společnost Microsoft neposkytuje žádné záruky, ať už výslovně nebo mlčky předpokládané, s ohledem na informace uvedené dále. Změny mohou být zavedeny kdykoli, před časem GA, v době GA nebo po.

Tyto definice můžou vaše vlastní zásady i zásada B2C_1A_base_extensions přepsat a tuto nadřazenou zásadu můžete podle potřeby vymezit tím, že jim poskytnete další.

Základními prvky *zásad B2C_1A_base* jsou typy deklarací identity, transformace deklarací identity a definice obsahu. Na tyto prvky je možné odkazovat ve vlastních zásadách a také v *zásadách B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Schémata deklarací identity

Tato schémata deklarací identity jsou rozdělená na tři části:

1.  V první části jsou uvedeny minimální deklarace identity, které jsou nutné k řádnému fungování cest uživatele.
2.  Druhá část obsahuje seznam deklarací identity potřebných pro parametry řetězce dotazu a další speciální parametry, které mají být předány jiným poskytovatelům deklarací identity, obzvláště login.microsoftonline.com pro ověřování. **Neměňte prosím tyto deklarace**.
3.  A nakonec třetí část, která obsahuje seznam dalších volitelných deklarací identity, které se dají shromažďovat od uživatele, uložené v adresáři a posílat v tokenech během přihlašování. V této části se dá přidat nový typ deklarací identity, který se má shromažďovat z uživatele nebo odeslat v tokenu.

> [!IMPORTANT]
> Schéma deklarací identity obsahuje omezení u určitých deklarací identity, jako jsou hesla a uživatelská jména. Zásady pro Trust Framework (TF) považují službu Azure AD za jiného zprostředkovatele deklarací identity a všechna jeho omezení jsou ve vlastních zásadách. Zásady je možné upravit tak, aby bylo možné přidat další omezení, nebo použít jiného zprostředkovatele deklarací identity pro úložiště přihlašovacích údajů, která budou mít vlastní omezení.

Dostupné typy deklarací identity jsou uvedené níže.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Deklarace identity, které jsou požadovány pro cestu uživatele

Pro správné fungování cest uživatelů jsou vyžadovány následující deklarace identity uživatele:

| Typ deklarací identity | Popis |
|-------------|-------------|
| *UserId* | Uživatelské jméno |
| *signInName* | Přihlašovací jméno |
| *tenantId* | Identifikátor tenanta (ID) objektu uživatele v Azure AD B2C |
| *Objektu* | Identifikátor objektu (ID) objektu uživatele v Azure AD B2C |
| *Heslo* | Heslo |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Zásady hesel používané Azure AD B2C k určení síly, vypršení platnosti hesla atd. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Telefonní číslo uživatele |
| *Verified.strongAuthenticationPhoneNumber* | |
| *elektron* | E-mailová adresa, která se dá použít ke kontaktování uživatele |
| *signInNamesInfo.emailAddress* | E-mailová adresa, kterou může uživatel použít k přihlášení |
| *otherMails* | E-mailové adresy, které se dají použít ke kontaktování uživatele |
| *userPrincipalName* | Uživatelské jméno, jak je uloženo v Azure AD B2C |
| *upnUserName* | Uživatelské jméno pro vytvoření hlavního názvu uživatele |
| *mailNickName* | Název e-mailové zprávy uživatele, jak je uložený v Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Deklarace identity, která určuje, jestli se atributy od uživatele shromáždily |
| *executed-PhoneFactor-Input* | Deklarace identity, která určuje, jestli se od uživatele shromáždilo nové telefonní číslo |
| *authenticationSource* | Určuje, jestli byl uživatel ověřený na poskytovateli sociálních identit, v login.microsoftonline.com nebo místním účtu. |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Deklarace identity vyžadované pro parametry řetězce dotazu a další speciální parametry

Následující deklarace identity se vyžadují pro předávání speciálních parametrů (včetně některých parametrů řetězce dotazu) jiným zprostředkovatelům deklarací identity:

| Typ deklarací identity | Popis |
|-------------|-------------|
| *nux* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *nca* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *prompt* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *mkt* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *lc* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *grant_type* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *oboru* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *client_id* | Speciální parametr předaný pro ověřování místního účtu do login.microsoftonline.com |
| *objectIdFromSession* | Parametr poskytovaný výchozím zprostředkovatelem správy relace, který označuje, že ID objektu bylo načteno z relace jednotného přihlašování. |
| *isActiveMFASession* | Parametr poskytovaný správou relací MFA k označení toho, že má uživatel aktivní relaci MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Další (volitelné) deklarace identity, které se dají shromáždit

Následující deklarace identity jsou další deklarace identity, které je možné shromáždit od uživatelů, které jsou uložené v adresáři a které se odesílají v tokenu. Jak je uvedeno dříve, do tohoto seznamu lze přidat další deklarace identity.

| Typ deklarací identity | Popis |
|-------------|-------------|
| *givenName* | Křestní jméno uživatele (označuje se také jako křestní jméno) |
| *příjmení* | Příjmení uživatele (označuje se také jako název rodiny nebo příjmení) |
| *Extension_picture* | Obrázek uživatele ze sociálních sítí |

## <a name="claim-transformations"></a>Transformace deklarací identity

Dostupné transformace deklarací identity jsou uvedené níže.

| Transformace deklarace identity | Popis |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definice obsahu

Tato část popisuje definice obsahu již deklarované v zásadách *B2C_1A_base* . Na tyto definice obsahu se dá odkazovat, přepsat nebo rozšířit podle potřeby ve vašich vlastních zásadách i v zásadách *B2C_1A_base_extensions* .

| Zprostředkovatel deklarací identity | Popis |
|-----------------|-------------|
| *Facebook* | |
| *Přihlášení k místnímu účtu* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *S vlastním uplatněním* | |
| *Místní účet* | |
| *Správa relací* | |
| *Modul zásad Trustframework* | |
| *TechnicalProfiles* | |
| *Vydavatel tokenů* | |

## <a name="technical-profiles"></a>Technické profily

Tato část popisuje technické profily, které jsou už deklarované na základě zprostředkovatele deklarací identity v zásadách *B2C_1A_base* . Tyto technické profily jsou náchylné k dalšímu odkazování, přepsání nebo rozšíření podle potřeby ve vašich vlastních zásadách i v zásadách *B2C_1A_base_extensions* .

### <a name="technical-profiles-for-facebook"></a>Technické profily pro Facebook

| Technický profil | Popis |
|-------------------|-------------|
| *Facebook – OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Technické profily pro přihlášení k místnímu účtu

| Technický profil | Popis |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Technické profily pro telefonní faktor

| Technický profil | Popis |
|-------------------|-------------|
| *PhoneFactor – vstup* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Technické profily pro Azure Active Directory

| Technický profil | Popis |
|-------------------|-------------|
| *AAD-Common* | Technický profil obsažený v dalších technických profilech AAD-XXX |
| *AAD-UserWriteUsingAlternativeSecurityId* | Technický profil pro přihlášení přes sociální sítě |
| *AAD-UserReadUsingAlternativeSecurityId* | Technický profil pro přihlášení přes sociální sítě |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Technický profil pro přihlášení přes sociální sítě |
| *AAD-UserWritePasswordUsingLogonEmail* | Technický profil pro místní účty |
| *AAD-UserReadUsingEmailAddress* | Technický profil pro místní účty |
| *AAD-UserWriteProfileUsingObjectId* | Technický profil pro aktualizaci záznamu uživatele pomocí objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Technický profil pro aktualizaci záznamu uživatele pomocí objectId |
| *AAD-UserWritePasswordUsingObjectId* | Technický profil pro aktualizaci záznamu uživatele pomocí objectId |
| *AAD-UserReadUsingObjectId* | Technický profil se používá ke čtení dat po ověření uživatele. |

### <a name="technical-profiles-for-self-asserted"></a>Technické profily pro samotný kontrolní výraz

| Technický profil | Popis |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Technické profily pro místní účet

| Technický profil | Popis |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Technické profily pro správu relací

| Technický profil | Popis |
|-------------------|-------------|
| *SM – NoOp* | |
| *SM – AAD* | |
| *SM – SocialSignup* | Název profilu slouží k jednoznačnému odstranění relace AAD mezi registrací a přihlášením. |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Technické profily pro modul zásad pro Trust Framework

V současné době nejsou definovány žádné technické profily pro zprostředkovatele deklarací identity **TechnicalProfiles Engine Trustframework Policy** .

### <a name="technical-profiles-for-token-issuer"></a>Technické profily pro vystavitele tokenů

| Technický profil | Popis |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Cesty uživatelů

Tato část popisuje cesty uživatelů již deklarované v zásadách *B2C_1A_base* . Tyto cesty uživatelů jsou náchylné k dalšímu odkazování, přepsání nebo rozšíření podle potřeby ve vašich vlastních zásadách i v zásadách *B2C_1A_base_extensions* .

| Cesta uživatele | Popis |
|--------------|-------------|
| *ISP* | |
| *Přihlášení* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
