---
title: Zůstat přihlášeni v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak nastavit si zachovat mě podepsán v (políčko zůstat Přihlášeni) v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e2aa52e8ad19274d45f648978e7b2f021139fe4a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812293"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Povolení možnosti zůstat přihlášeni v (políčko zůstat Přihlášeni) v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete povolit funkci zachovat mě podepsán v (políčko zůstat Přihlášeni) pro vaše webové a nativní aplikace v Azure Active Directory (Azure AD) B2C. Tato funkce uděluje přístup k vrácení uživatelé aplikaci bez zobrazení výzvy zadejte své uživatelské jméno a heslo. Tento přístup je odvolat při odhlášení uživatele. 

Uživatelé neměli byste povolit tuto možnost na veřejné počítače. 

![Povolení možnosti zůstat přihlášeni](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Požadavky

Tenanta služby Azure AD B2C, který je nakonfigurovaný pro místní účet povolit, zaregistrujte se a přihlaste se. Pokud nemáte tenanta, můžete vytvořit pomocí postupu v [kurzu: Vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Přidat definici obsahu elementu 

V části **BuildingBlocks** element příponu souboru, přidejte **ContentDefinitions** elementu. 

1. V části **ContentDefinitions** elementu, přidejte **ContentDefinition** element s identifikátorem `api.signuporsigninwithkmsi`.
2. V části **ContentDefinition** prvku, přidejte **LoadUri**, **RecoveryUri**, a **parametr** elementy. `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` Hodnotu **parametr** element je srozumitelné identifikátor počítače, který se zobrazí zaškrtávací políčko políčko zůstat Přihlášeni v přihlašovací stránky. Tato hodnota nesmí být změněna. 

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

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Přidat zprostředkovatele deklarací identity přihlášení pro místní účet  

Můžete definovat přihlášení místním účtem jako zprostředkovatele deklarací identity pomocí **ClaimsProvider** prvku v souboru rozšíření zásady:

1. Otevřít *TrustFrameworkExtensions.xml* souboru z pracovního adresáře. 
2. Najít **ClaimsProviders** elementu. Pokud neexistuje, přidejte jej pod kořenovým elementem. [Starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) zahrnuje poskytovatele deklarací identity přihlášení místní účet. 
3. Přidat **ClaimsProvider** křížkem **DisplayName** a **technický profil** jak je znázorněno v následujícím příkladu:

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

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Přidání identifikátorů aplikací do vlastní zásady

Přidat identifikátory aplikace *TrustFrameworkExtensions.xml* souboru.

1. V *TrustFrameworkExtensions.xml* souboru, vyhledejte **technický profil** element s identifikátor `login-NonInteractive` a **technický profil** element s identifikátor typu `login-NonInteractive-PasswordChange` a nahradit všechny hodnoty `IdentityExperienceFrameworkAppId` s identifikátorem aplikace architekturu rozhraní identit aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Nahraďte všechny hodnoty `ProxyIdentityExperienceFrameworkAppId` s identifikátorem aplikace architekturu rozhraní identit Proxy aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).
3. Uložte soubor rozšíření.

## <a name="create-a-kmsi-enabled-user-journey"></a>Vytvořit cestu povoleného uživatele políčko zůstat Přihlášeni

Přidáte zprostředkovatele deklarací identity přihlášení pro místní účet do vaší cesty uživatele. 

1. Otevřete soubor základní zásady. Například *TrustFrameworkBase.xml*.
2. Najít **Userjourney** elementu a zkopírujte celý obsah **UserJourney** element, který používá identifikátor `SignUpOrSignIn`.
3. Otevřete soubor rozšíření. Například *TrustFrameworkExtensions.xml* a najít **Userjourney** elementu. Pokud element neexistuje, přidejte jeden.
4. Vložit celé **UserJourney** element, který jste zkopírovali jako podřízený objekt **Userjourney** elementu.
5. Změňte hodnotu identifikátoru pro cestu nového uživatele. Například, `SignUpOrSignInWithKmsi`.
6. A konečně, změňte hodnotu vlastnosti v prvním kroku Orchestrace **ContentDefinitionReferenceId** k `api.signuporsigninwithkmsi`. Nastavení této hodnoty umožňuje zaškrtávací políčko v cestě uživatele. 
7. Uložte a odešlete soubor rozšíření a ujistěte se, že všechny ověření úspěšné.

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

## <a name="create-a-relying-party-file"></a>Vytvořit soubor předávající strany

Aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* souborů ve vašem pracovním adresáři a přejmenujte jej. Například *SignUpOrSignInWithKmsi.xml*.
2. Otevřete nový soubor a aktualizace **PolicyId** atribut pro **TrustFrameworkPolicy** s jedinečnou hodnotu. Toto je název zásady. Například, `SignUpOrSignInWithKmsi`.
3. Změnit **ReferenceId** atribut pro **DefaultUserJourney** element tak, aby odpovídaly identifikátor nové cesty uživatele, který jste vytvořili. Například, `SignUpOrSignInWithKmsi`.

    Políčko zůstat Přihlášeni je nakonfigurovaný nástrojem **UserJourneyBehaviors** element s **SingleSignOn**, **sessionexpirytype pro**, a **SessionExpiryInSeconds** jako svůj první podřízené prvky. **KeepAliveInDays** atribut určuje, jak dlouho zůstane přihlášený uživatel. V následujícím příkladu, políčko zůstat Přihlášeni relace automaticky vyprší po `7` dnů bez ohledu na to, kolikrát uživatel provádí bezobslužné ověření. Nastavení **KeepAliveInDays** hodnota, která se `0` vypne funkce políčko zůstat Přihlášeni. Ve výchozím nastavení, tato hodnota je `0`. Pokud hodnota **sessionexpirytype pro** je `Rolling`, je políčko zůstat Přihlášeni relace prodloužena `7` dnů pokaždé, když uživatel provádí bezobslužné ověření.  Pokud `Rolling` je vybrána, byste měli mít počet dní, minimální. 

    Hodnota **SessionExpiryInSeconds** představuje čas vypršení platnosti relace jednotného přihlašování. To se používá interně službou Azure AD B2C ke kontrole, jestli je platnost relace pro políčko zůstat Přihlášeni, nebo ne. Hodnota **KeepAliveInDays** určuje platnost vyprší, Max-Age hodnotu souboru cookie jednotného přihlašování ve webovém prohlížeči. Na rozdíl od **SessionExpiryInSeconds**, **KeepAliveInDays** umožňuje zabránit uzavřený vymazání souboru cookie prohlížeče. Uživatel může bez upozornění přihlásit jenom v případě, že existuje soubor cookie relace jednotného přihlašování, které řídí **KeepAliveInDays**a ne vypršela platnost, která řídí **SessionExpiryInSeconds**. 
    
    Pokud uživatel nemá povolení **neodhlašovat** na stránce registrace a přihlášení relace vyprší po času indikován **SessionExpiryInSeconds** uplynutí nebo prohlížeč je zavřený. Pokud uživatel povolí **neodhlašovat**, hodnota **KeepAliveInDays** přepíše hodnotu **SessionExpiryInSeconds** a určí čas vypršení platnosti relace. Dokonce i uživatelé zavřete prohlížeč a znovu otevřete, se můžete stále tiše přihlásit, dokud je v době **KeepAliveInDays**. Doporučuje se, že nastavíte hodnotu **SessionExpiryInSeconds** krátká období (1 200 sekund), při hodnotu **KeepAliveInDays** lze nastavit relativně dlouhou dobu (7 dní), jak je znázorněno Následující příklad:

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

4. Uložte změny a pak nahrajte soubor.
5. Vlastní zásady, které jste nahráli, otestovat na webu Azure Portal, přejděte na stránku zásad a pak vyberte **spustit nyní**.

Můžete najít ukázkové zásady [tady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








