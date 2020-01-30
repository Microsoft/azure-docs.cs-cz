---
title: Zůstat přihlášeni v Azure Active Directory B2C
description: Naučte se, jak nastavit políčko zůstat přihlášeni (keep-signed) v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 052e1bc4e9a14b34e21b0bfeb4193fbd0b2b1a22
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848899"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Povolit možnost zůstat přihlášeni (políčko zůstat přihlášeni) v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete povolit funkci zůstat přihlášeni (políčko zůstat přihlášeni) uživatelům vaší webové a nativní aplikace, které mají místní účty v adresáři Azure Active Directory B2C (Azure AD B2C). Tato funkce uděluje přístup uživatelům vraceným do vaší aplikace bez výzvy k zadání uživatelského jména a hesla. Tento přístup se odvolá, když se uživatel odhlásí.

Uživatelé by tuto možnost neměli na veřejných počítačích povolit.

![Příklad přihlašovací stránky pro přihlášení, která zobrazuje zaškrtávací políčko zůstat přihlášeni](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Požadavky

Tenant Azure AD B2C, který je nakonfigurovaný tak, aby povoloval přihlášení k místnímu účtu. POLÍČKO zůstat přihlášeni není pro externí účty zprostředkovatele identity podporován.

Pokud tenanta nemáte, můžete ho vytvořit pomocí kroků v části [kurz: vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Přidat element definice obsahu

V rámci elementu **BuildingBlocks** souboru rozšíření přidejte element **ContentDefinitions** .

1. V rámci elementu **ContentDefinitions** přidejte element **ContentDefinition** s identifikátorem `api.signuporsigninwithkmsi`.
2. V rámci elementu **ContentDefinition** přidejte elementy **LoadUri**, **RecoveryUri**a **DataUri** . Hodnota `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` elementu **DataUri** je strojově srozumitelný identifikátor, který na přihlašovacích stránkách přinese ZAŠKRTÁVACÍ políčko políčko zůstat přihlášeni. Tato hodnota nesmí být změněna.

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Přidání zprostředkovatele deklarací přihlašování pro místní účet

Přihlášení místního účtu jako zprostředkovatele deklarací můžete definovat pomocí elementu **ClaimsProvider** v souboru rozšíření zásady:

1. Otevřete soubor *TrustFrameworkExtensions. XML* z pracovního adresáře.
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element. [Úvodní sada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) obsahuje zprostředkovatele deklarací přihlášeného k místnímu účtu.
3. Přidejte element **ClaimsProvider** s **DisplayName** a **TechnicalProfile** , jak je znázorněno v následujícím příkladu:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Přidejte identifikátory aplikace do vlastních zásad.

Přidejte identifikátory aplikace do souboru *TrustFrameworkExtensions. XML* .

1. V souboru *TrustFrameworkExtensions. XML* vyhledejte prvek **TechnicalProfile** s identifikátorem `login-NonInteractive` a element **TechnicalProfile** s identifikátorem `login-NonInteractive-PasswordChange` a nahraďte všechny hodnoty `IdentityExperienceFrameworkAppId` identifikátorem aplikace rozhraní identity Experience Framework, jak je popsáno v tématu [Začínáme](custom-policy-get-started.md).

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Nahraďte všechny hodnoty `ProxyIdentityExperienceFrameworkAppId` identifikátorem aplikace architektury proxy serveru rozhraní identity, jak je popsáno v tématu [Začínáme](custom-policy-get-started.md).
3. Uložte soubor rozšíření.

## <a name="create-a-kmsi-enabled-user-journey"></a>Vytvoření cesty uživatele s povoleným políčko zůstat přihlášeni

Přidejte zprostředkovatele deklarací přihlašování pro místní účet k cestě uživatele.

1. Otevřete základní soubor zásad. Například *TrustFrameworkBase. XML*.
2. Vyhledejte element **userjourney** a zkopírujte celý obsah elementu **UserJourney** , který používá identifikátor `SignUpOrSignIn`.
3. Otevřete soubor rozšíření. Například *TrustFrameworkExtensions. XML* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý element **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Změňte hodnotu identifikátoru pro novou cestu uživatele. Například, `SignUpOrSignInWithKmsi`.
6. Nakonec v prvním kroku orchestrace změňte hodnotu **ContentDefinitionReferenceId** na `api.signuporsigninwithkmsi`. Nastavení této hodnoty povolí v cestě uživatele zaškrtávací políčko.
7. Uložte a nahrajte soubor rozšíření a ujistěte se, že všechna ověření proběhla úspěšně.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Vytvoření souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii souboru *SignUpOrSignIn. XML* v pracovním adresáři a přejmenujte jej. Například *SignUpOrSignInWithKmsi. XML*.
2. Otevřete nový soubor a aktualizujte atribut **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Toto je název vaší zásady. Například, `SignUpOrSignInWithKmsi`.
3. Změňte atribut **ReferenceId** elementu **DefaultUserJourney** tak, aby odpovídal identifikátoru nové cesty pro uživatele, kterou jste vytvořili. Například, `SignUpOrSignInWithKmsi`.

    POLÍČKO zůstat přihlášeni je nakonfigurována pomocí elementu **UserJourneyBehaviors** s **SingleSignon**, **SessionExpiryType**a **SessionExpiryInSeconds** jako své první podřízené prvky. Atribut **KeepAliveInDays** určuje, jak dlouho zůstane uživatel přihlášený. V následujícím příkladu políčko zůstat přihlášeni relace automaticky vyprší po `7` dnech bez ohledu na to, jak často uživatel provádí tiché ověřování. Nastavením hodnoty **KeepAliveInDays** na `0` vypnete funkci políčko zůstat přihlášeni. Ve výchozím nastavení je tato hodnota `0`. Pokud je hodnota **SessionExpiryType** `Rolling`, relace políčko zůstat přihlášeni se rozšíří o `7` dnů pokaždé, když uživatel provede tiché ověřování.  Pokud je vybraná možnost `Rolling`, měli byste zachovat počet dní na minimum.

    Hodnota **SessionExpiryInSeconds** představuje čas vypršení platnosti relace jednotného přihlašování. Používá se interně Azure AD B2C ke kontrole, jestli vypršela platnost relace pro políčko zůstat přihlášeni. Hodnota **KeepAliveInDays** Určuje hodnotu vypršení platnosti souboru cookie jednotného přihlašování (SSO) ve webovém prohlížeči. Na rozdíl od **SessionExpiryInSeconds**se **KeepAliveInDays** používá k tomu, aby se zabránilo vymazávání souboru cookie v prohlížeči, když je zavřený. Uživatel se může bez upozornění přihlásit jenom v případě, že existuje soubor cookie relace jednotného přihlašování, který je řízený **KeepAliveInDays**a nevypršela jeho platnost, kterou řídí **SessionExpiryInSeconds**.

    Pokud uživatel nepovolí možnost **zůstat přihlášeni** na přihlašovací stránce a přihlašovací stránku, platnost relace vyprší po uplynutí doby uvedené v **SessionExpiryInSeconds** nebo při zavření prohlížeče. Pokud uživatel povolí možnost **zůstat přihlášeni**, hodnota **KeepAliveInDays** přepíše hodnotu **SessionExpiryInSeconds** a vyhodnotí dobu vypršení platnosti relace. I když uživatelé zavřou prohlížeč a otevřou ho znovu, můžou se pořád tiše přihlásit, dokud bude v době **KeepAliveInDays**. Doporučuje se nastavit hodnotu **SessionExpiryInSeconds** na krátkou dobu (1200 sekund), zatímco hodnota **KeepAliveInDays** může být nastavená na poměrně dlouhou dobu (7 dnů), jak je znázorněno v následujícím příkladu:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
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
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Uložte změny a pak soubor nahrajte.
5. Pokud chcete otestovat vlastní zásady, které jste nahráli, na stránce Azure Portal klikněte na stránku zásady a potom vyberte **Spustit nyní**.

Ukázkovou zásadu najdete [tady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
