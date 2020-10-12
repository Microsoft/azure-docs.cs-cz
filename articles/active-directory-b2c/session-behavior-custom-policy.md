---
title: Konfigurace chování relace pomocí vlastních zásad – Azure Active Directory B2C | Microsoft Docs
description: Nakonfigurujte chování relace pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2f20a4521efe2806c4bc66e4612b99caf84382a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385259"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace chování relace pomocí vlastních zásad v Azure Active Directory B2C

Správa [relací jednotného přihlašování (SSO)](session-overview.md) v Azure Active Directory B2C (Azure AD B2C) umožňuje správci řídit interakci s uživatelem poté, co uživatel již ověřený. Správce může například řídit, jestli se zobrazuje výběr zprostředkovatelů identity, nebo jestli je potřeba zadat podrobnosti o účtu znovu. Tento článek popisuje, jak nakonfigurovat nastavení jednotného přihlašování pro Azure AD B2C.

## <a name="session-behavior-properties"></a>Vlastnosti chování relace

Pomocí následujících vlastností můžete spravovat relace webových aplikací:

- **Doba života relace webové aplikace (minuty)** – doba života souboru cookie relace Azure AD B2C's, která se po úspěšném ověření uloží v prohlížeči uživatele.
    - Výchozí hodnota = 86400 sekund (1440 minut).
    - Minimální (včetně) = 900 sekund (15 minut).
    - Maximální (včetně) = 86400 sekund (1440 minut).
- **Časový limit relace webové aplikace** – [Typ vypršení platnosti relace](session-overview.md#session-expiry-type), *návratová*nebo *absolutní*. 
- **Konfigurace jednotného přihlašování** – [rozsah relace](session-overview.md#session-scope) chování jednotného přihlašování (SSO) v rámci více aplikací a toků uživatelů ve vašem Azure AD B2C klientovi. 

## <a name="configure-the-properties"></a>Konfigurace vlastností

Chcete-li změnit chování relace a konfigurace jednotného přihlašování, přidejte do elementu [RelyingParty](relyingparty.md) element **UserJourneyBehaviors** .  Element **UserJourneyBehaviors** musí následovat za **DefaultUserJourney**. Váš element **UserJourneyBehavors** by měl vypadat jako v tomto příkladu:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>Jednotné odhlašování

### <a name="configure-the-applications"></a>Konfigurace aplikací

Když uživatele přesměrujete na koncový bod Azure AD B2Cho odhlašování (pro protokoly OAuth2 i SAML), Azure AD B2C vymaže relaci uživatele z prohlížeče.  Pro povolení [jednotného odhlašování](session-overview.md#single-sign-out)nastavte `LogoutUrl` aplikaci z Azure Portal:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Kliknutím na svůj účet v pravém horním rohu stránky vyberte svůj Azure AD B2C adresář.
1. V nabídce vlevo zvolte **Azure AD B2C**, vyberte **Registrace aplikací**a pak vyberte svou aplikaci.
1. Vyberte **Nastavení**, vyberte **vlastnosti**a pak vyhledejte textové pole **odhlašovací adresa URL** . 

### <a name="configure-the-token-issuer"></a>Konfigurace vystavitele tokenů 

Aby bylo možné podporovat jednotné přihlašování, musí technické profily vystavitele tokenů pro tokeny JWT i SAML určovat:

- Název protokolu, například `<Protocol Name="OpenIdConnect" />`
- Odkaz na technický profil relace, například `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Následující příklad znázorňuje vystavitele tokenů JWT a SAML s jednotným odhlášením:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure AD B2C relaci](session-overview.md).
