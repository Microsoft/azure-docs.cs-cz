---
title: Konfigurace změny hesla pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Naučte se, jak uživatelům povolit změnu hesla pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cd63144074577d4ff3564da41e672dd1ca226dcb
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257156"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace změny hesla pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

V Azure Active Directory B2C (Azure AD B2C) můžete uživatelům, kteří jsou přihlášení pomocí místního účtu, povolit změnu hesla, aniž by museli prokázat svoji identitu prostřednictvím ověření e-mailu. Tok změny hesla zahrnuje následující kroky:

1. Uživatel se přihlásí ke svému místnímu účtu. Pokud je relace stále aktivní, Azure AD B2C autorizuje uživatele a přeskočí k dalšímu kroku.
1. Uživatel ověří **staré heslo** a pak vytvoří a potvrdí **nové heslo**.

![Tok změny hesla](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Tok změny hesla umožňuje uživatelům změnit heslo pouze v případě, že uživatel zná heslo a chce ho změnit. Doporučujeme také povolit [Samoobslužné resetování hesla](add-password-reset-policy.md) , aby bylo možné podporovat případy, kdy uživatel zapomene heslo.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Požadavky

* Proveďte kroky v části Začínáme [s vlastními zásadami v Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).
* Pokud jste to ještě neudělali, [Zaregistrujte webovou aplikaci v Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Přidat elementy

1. Otevřete soubor *TrustframeworkExtensions.xml* a přidejte následující element **ClaimType** s identifikátorem `oldPassword` do prvku [ClaimsSchema](claimsschema.md) :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Element [ClaimsProvider](claimsproviders.md) obsahuje technický profil, který uživatele ověřuje. Do prvku **ClaimsProviders** přidejte následující zprostředkovatele deklarací identity:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

3. Element [UserJourney](userjourneys.md) definuje cestu, kterou uživatel provede při interakci s aplikací. Přidejte element **userjourney** , pokud neexistuje s **UserJourney** identifikovaným jako `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Uložte soubor zásad *TrustFrameworkExtensions.xml* .
5. Zkopírujte soubor *ProfileEdit.xml* , který jste stáhli pomocí úvodní sady, a pojmenujte ho *ProfileEditPasswordChange.xml*.
6. Otevřete nový soubor a aktualizujte atribut **PolicyId** o jedinečnou hodnotu. Tato hodnota je název vaší zásady. Například *B2C_1A_profile_edit_password_change*.
7. Upravte atribut **ReferenceId** v nástroji `<DefaultUserJourney>` tak, aby odpovídal ID nové cesty uživatele, kterou jste vytvořili. Například *PasswordChange*.
8. Uložte provedené změny.

## <a name="upload-and-test-the-policy"></a>Nahrávání a testování zásad

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
5. Na stránce vlastní zásady klikněte na **nahrát zásadu**.
6. Vyberte **přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustframeworkExtensions.xml* .
7. Klikněte na **Odeslat**.
8. Opakujte kroky 5 až 7 pro soubor předávající strany, například *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Spustit zásadu

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_profile_edit_password_change*.
2. V případě **aplikace** vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pro zobrazení tokenu by se měla zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Klikněte na **Spustit**. Přihlaste se pomocí účtu, který jste vytvořili dříve. Nyní byste měli mít možnost změnit heslo.

## <a name="next-steps"></a>Další kroky

- Vyhledejte ukázkové zásady na [GitHubu](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Přečtěte si, jak můžete [nakonfigurovat složitost hesla v Azure AD B2C](password-complexity.md).
- Nastavte [tok resetování hesla](add-password-reset-policy.md).

::: zone-end
