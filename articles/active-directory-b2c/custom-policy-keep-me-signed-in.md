---
title: Měnechat si přihlášenve službě Azure Active Directory B2C
description: Přečtěte si, jak nastavit keep me signed in (KMSI) ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041fb8d881307b52fb170a11618f930debc522a4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803156"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Povolit, aby mě přihlášení (KMSI) ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete povolit keep me signed in (KMSI) funkce pro uživatele webu a nativní aplikace, které mají místní účty ve vašem adresáři Azure Active Directory B2C (Azure AD B2C). Tato funkce uděluje přístup uživatelům, kteří se vracejí do vaší aplikace, aniž by jim byla výzva k opětovnému zadání uživatelského jména a hesla. Tento přístup je odvolán, když se uživatel odhlásí.

Uživatelé by neměli tuto možnost povolit ve veřejných počítačích.

![Příklad přihlašovací stránky pro přihlášení s políčkem Nechat mě přihlášeného](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Požadavky

- Klient Azure AD B2C, který je nakonfigurovaný tak, aby umožňoval přihlášení k místnímu účtu. KMSI není podporovánpro účty externích zprostředkovatelů identity.
- Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Konfigurace identifikátoru stránky

Chcete-li povolit `DataUri` KMSI, nastavte prvek definice obsahu na identifikátor `unifiedssp` [stránky](contentdefinitions.md#datauri) a [stránku verze](page-layout.md) *1.1.0* nebo vyšší.

1. Otevřete soubor rozšíření zásad. Například <em> `SocialAndLocalAccounts/` </em>. Tento soubor rozšíření je jedním ze souborů zásad zahrnutých do vlastního balíčku zásad, který byste měli získat v předpokladu [Začínáme s vlastními zásadami](custom-policy-get-started.md).
1. Vyhledejte element **BuildingBlocks.** Pokud prvek neexistuje, přidejte jej.
1. Přidejte **contentdefinitions** prvek **BuildingBlocks** prvek zásady.

    Vaše vlastní zásady by měly vypadat jako následující fragment kódu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Přidání metadat do samoobslužného technického profilu

Chcete-li přidat zaškrtávací políčko KMSI na `setting.enableRememberMe` přihlašovací a přihlašovací stránku, nastavte metadata na hodnotu true. Přepsat SelfAsserted-LocalAccountSignin-Email technické profily v souboru rozšíření.

1. Najít ClaimsProviders element. Pokud prvek neexistuje, přidejte jej.
1. Do prvku ClaimsProviders přidejte následujícího zprostředkovatele deklarací identity:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Uložte soubor přípon.

## <a name="configure-a-relying-party-file"></a>Konfigurace souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Otevřete vlastní soubor zásad. Například *SignUpOrSignin.xml*.
1. Pokud ještě neexistuje, přidejte `<UserJourneyBehaviors>` do `<RelyingParty>` uzlu podřízený uzel. Musí být umístěn `<DefaultUserJourney ReferenceId="User journey Id" />`bezprostředně po `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`, například: .
1. Přidejte následující uzel jako podřízený `<UserJourneyBehaviors>` prvek.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Označuje, jak je relace `SessionExpiryInSeconds` prodloužena o čas určený v a `KeepAliveInDays`. Hodnota `Rolling` (výchozí) označuje, že relace je rozšířena pokaždé, když uživatel provádí ověřování. Hodnota `Absolute` označuje, že uživatel je nucen znovu ověřit po zadaném časovém období.

    - **SessionExpiryInSeconds** - Životnost souborů cookie relace při *zachování přihlášení* není povolena nebo pokud uživatel nevybere *políčko ponechat mě přihlášeného*. Platnost relace vyprší `SessionExpiryInSeconds` po uplynutí nebo je prohlížeč uzavřen.

    - **KeepAliveInDays** - Životnost relačních souborů cookie při *přihlášení* je povolena a uživatel *vybere, aby mě udržela přihlášena*.  Hodnota `KeepAliveInDays` má přednost před `SessionExpiryInSeconds` hodnotou a určuje čas vypršení platnosti relace. Pokud uživatel zavře prohlížeč a znovu jej otevře později, může stále tiše přihlásit tak dlouho, dokud je v časovém období KeepAliveInDays.

    Další informace naleznete v [tématu chování cesty uživatele](relyingparty.md#userjourneybehaviors).

Doporučujeme nastavit hodnotu SessionExpiryInSeconds být krátké období (1200 sekund), zatímco hodnota KeepAliveInDays lze nastavit na relativně dlouhé období (30 dní), jak je znázorněno v následujícím příkladu:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Otestujte své zásady

1. Uložte změny a nahrajte soubor.
1. Pokud chcete otestovat vlastní zásady, které jste nahráli, přejděte na webu Azure Portal na stránku zásad a vyberte **Spustit .**
1. Zadejte **své uživatelské jméno** a **heslo**, vyberte **Ponechat přihlášena**a klikněte na **přihlásit**se .
1. Vraťte se na Azure Portal. Přejděte na stránku zásad a pak vyberte **Kopírovat,** chcete-li zkopírovat přihlašovací adresu URL.
1. V adresním řádku prohlížeče `&prompt=login` odeberte parametr řetězce dotazu, který vynutí, aby uživatel při tomto požadavku zadá svá pověření.
1. V prohlížeči klikněte na **Přejít**. Teď Azure AD B2C vydá přístupový token bez výzvy k přihlášení znovu. 

## <a name="next-steps"></a>Další kroky

Ukázkové zásady naleznete [zde](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
