---
title: Nakonfigurujte složitost hesla pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak konfigurovat požadavky na složitost hesla pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 74542f86d5114ff57e358db7e239e307059fe5ad
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580344"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Nakonfigurujte složitost hesla pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory (Azure AD) B2C můžete konfigurovat požadavky na složitost hesla, které jsou k dispozici uživatelem při vytváření účtu. Ve výchozím nastavení, Azure AD B2C používá **silné** hesla. Tento článek ukazuje, jak nakonfigurovat složitost hesla v [vlastní zásady](active-directory-b2c-overview-custom.md). Je také možné nakonfigurovat jako složitost hesla používal v [toky uživatelů](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [začít pracovat s vlastními zásadami v Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Přidat prvky

1. Kopírovat *SignUpOrSignIn.xml* soubor stáhne společně s starter pack a pojmenujte ho *SingUpOrSignInPasswordComplexity.xml*.
2. Otevřít *SingUpOrSignInPasswordComplexity.xml* soubor a změňte **PolicyId** a **PublicPolicyUri** na nový název zásady. Například *B2C_1A_signup_signin_password_complexity*.
3. Přidejte následující **typu deklarace identity** prvky s identifikátory `newPassword` a `reenterPassword`:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [Predikáty](predicates.md) typů metodu `IsLengthRange` nebo `MatchesRegex`. `MatchesRegex` Typ se používá k odpovídají regulárnímu výrazu. `IsLengthRange` Typ přebírá řetězec minimální a maximální délku. Přidat **predikáty** elementu **BuildingBlocks** elementu, jestliže neexistuje následujícím **predikátu** prvky:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Každý **InputValidation** element je vytvořena s použitím určené **predikátu** elementy. Tento prvek umožňuje provádět logická agregace, které jsou podobné `and` a `or`. Přidat **InputValidations** elementu **BuildingBlocks** elementu, jestliže neexistuje následujícím **InputValidation** element:

    ```XML
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
    ```

6. Ujistěte se, že **PolicyProfile** technický profil obsahuje následující prvky:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Uložte soubor zásad.

## <a name="test-your-policy"></a>Vaše zásada testování

Při testování aplikací v Azure AD B2C, může být užitečné mít tokenu Azure AD B2C vrátí `https://jwt.ms` být schopni zkontrolovat deklarace identity v ní.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu rozhraní identit**.
5. Na stránce vlastní zásady, klikněte na tlačítko **nahrát zásady**.
6. Vyberte **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *SingUpOrSignInPasswordComplexity.xml* souboru.
7. Klikněte na **Odeslat**.

### <a name="run-the-policy"></a>Spustit zásady

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_signup_signin_password_complexity*.
2. Pro **aplikace**, vyberte aplikace, které jste dříve zaregistrovali. Chcete-li zobrazit token, **adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na **Spustit**.
4. Vyberte **zaregistrujte se hned teď**, zadejte e-mailovou adresu a zadejte nové heslo. Průvodce se zobrazí na omezení pro heslo. Dokončit zadávání informací o uživateli a potom klikněte na tlačítko **vytvořit**. Měli byste vidět obsah, který byl vrácen token.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [konfigurace Změna hesla pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


