---
title: Zůstat přihlášeni v Azure Active Directory B2C
description: Naučte se, jak nastavit políčko zůstat přihlášeni (keep-signed) v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5dd051804f3a0a7d1b0d32b998262af13e8926
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389186"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Povolit možnost zůstat přihlášeni (políčko zůstat přihlášeni) v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete povolit funkci zůstat přihlášeni (políčko zůstat přihlášeni) uživatelům vaší webové a nativní aplikace, které mají místní účty v adresáři Azure Active Directory B2C (Azure AD B2C). Tato funkce uděluje přístup uživatelům vraceným do vaší aplikace bez výzvy k zadání uživatelského jména a hesla. Tento přístup se odvolá, když se uživatel odhlásí.

Uživatelé by tuto možnost neměli na veřejných počítačích povolit.

![Příklad přihlašovací stránky pro přihlášení, která zobrazuje zaškrtávací políčko zůstat přihlášeni](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Požadavky

- Tenant Azure AD B2C, který je nakonfigurovaný tak, aby povoloval přihlášení k místnímu účtu. POLÍČKO zůstat přihlášeni není pro externí účty zprostředkovatele identity podporován.
- Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Konfigurace identifikátoru stránky

Chcete-li povolit políčko zůstat přihlášeni, nastavte element definice obsahu `DataUri` na [identifikátor stránky](contentdefinitions.md#datauri) `unifiedssp` a [verzi stránky](page-layout.md) *1.1.0* nebo vyšší.

1. Otevřete soubor rozšíření vašich zásad. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Tento soubor rozšíření je jedním ze souborů zásad, které jsou součástí úvodní sady Custom Policy Pack, které byste měli mít k dispozici v rámci svých požadavků. Začněte [s vlastními zásadami](custom-policy-get-started.md).
1. Vyhledejte element **BuildingBlocks** . Pokud element neexistuje, přidejte jej.
1. Přidejte element **ContentDefinitions** do elementu **BuildingBlocks** zásady.

    Vaše vlastní zásada by měla vypadat jako následující fragment kódu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Přidejte metadata do technického profilu s vlastním uplatněním.

Chcete-li přidat zaškrtávací políčko políčko zůstat přihlášeni do přihlašovací stránky a přihlašovací stránky, nastavte `setting.enableRememberMe` metadata na hodnotu true. V souboru rozšíření přepište technické profily SelfAsserted-LocalAccountSignin-email.

1. Vyhledejte element ClaimsProviders. Pokud element neexistuje, přidejte jej.
1. Do prvku ClaimsProviders přidejte následující zprostředkovatele deklarací identity:

```xml
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

1. Uložte soubor rozšíření.

## <a name="configure-a-relying-party-file"></a>Konfigurace souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Otevřete vlastní soubor zásad. Například *SignUpOrSignin.xml*.
1. Pokud ještě neexistuje, přidejte `<UserJourneyBehaviors>` do uzlu podřízený uzel `<RelyingParty>` . Musí být umístěn hned po `<DefaultUserJourney ReferenceId="User journey Id" />` , například: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Přidejte následující uzel jako podřízený `<UserJourneyBehaviors>` prvek elementu.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** – určuje, jak je relace rozšířena o čas zadaný v `SessionExpiryInSeconds` a  `KeepAliveInDays` . `Rolling`Hodnota (výchozí) znamená, že relace je rozšířena pokaždé, když uživatel provede ověření. `Absolute`Hodnota znamená, že se uživatel po uplynutí zadaného časového období nuceně znovu ověří.

    - **SessionExpiryInSeconds**  – doba života souborů cookie relací v případě, že je možnost *zůstat přihlášená* , není povolená nebo uživatel nevybere možnost *zůstat přihlášeni*. Platnost relace skončí po úspěšném dokončení `SessionExpiryInSeconds` nebo když je prohlížeč zavřený.

    - **KeepAliveInDays** – povolený počet souborů cookie relace, pokud je zapnutá možnost *zůstat přihlášení* a uživatel vybere možnost *zůstat přihlášeni*.  Hodnota `KeepAliveInDays` má přednost před `SessionExpiryInSeconds` hodnotou a určuje čas vypršení platnosti relace. Pokud uživatel zavře prohlížeč a později ho znovu otevře, může se stále tiše přihlásit, dokud bude v KeepAliveInDays časovém období.

    Další informace najdete v tématu [chování při cestě uživatele](relyingparty.md#userjourneybehaviors).

Doporučujeme nastavit hodnotu SessionExpiryInSeconds na krátkou dobu (1200 sekund), zatímco hodnota KeepAliveInDays se dá nastavit na poměrně dlouhou dobu (30 dnů), jak je znázorněno v následujícím příkladu:

```xml
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

## <a name="test-your-policy"></a>Testování zásad

1. Uložte změny a pak soubor nahrajte.
1. Pokud chcete otestovat vaše vlastní zásady, které jste nahráli, na stránce Azure Portal klikněte na stránku zásady a pak vyberte **Spustit nyní**.
1. Zadejte své **uživatelské jméno** a **heslo**, vyberte **zůstat přihlášeni**a potom klikněte na **Přihlásit**se.
1. Vraťte se na Azure Portal. Přejít na stránku zásady a potom výběrem možnosti **Kopírovat** zkopírujte přihlašovací adresu URL.
1. V adresním řádku prohlížeče odeberte `&prompt=login` parametr řetězce dotazu, který uživateli vynutí zadání přihlašovacích údajů k této žádosti.
1. V prohlížeči klikněte na **Přejít**. Nyní Azure AD B2C vydá přístupový token bez výzvy k přihlášení. 

## <a name="next-steps"></a>Další kroky

[Tady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)najdete ukázkovou zásadu.
