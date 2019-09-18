---
title: Konfigurace složitosti hesla pomocí vlastních zásad v Azure Active Directory B2C | Microsoft Docs
description: Jak nakonfigurovat požadavky na složitost hesla pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6454d380b0f34e940951e3de44d1dee0ff6b597f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065528"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace složitosti hesla pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory B2C (Azure AD B2C) můžete nakonfigurovat požadavky na složitost pro hesla, která jsou k dispozici uživatelem při vytváření účtu. Ve výchozím nastavení používá Azure AD B2C používání silných hesel. V tomto článku se dozvíte, jak nakonfigurovat složitost hesla ve [vlastních zásadách](active-directory-b2c-overview-custom.md). Je také možné nakonfigurovat složitost hesla v [uživatelských tocích](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami v Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Přidat elementy

1. Zkopírujte soubor *SignUpOrSignIn. XML* , který jste stáhli pomocí úvodní sady, a pojmenujte ho *SingUpOrSignInPasswordComplexity. XML*.
2. Otevřete soubor *SingUpOrSignInPasswordComplexity. XML* a změňte **PolicyId** a **PublicPolicyUri** na nový název zásady. Například *B2C_1A_signup_signin_password_complexity*.
3. Přidejte následující prvky **ClaimType** s identifikátory `newPassword` a `reenterPassword`:

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

4. [Predikáty](predicates.md) mají typy `IsLengthRange` metod nebo. `MatchesRegex` `MatchesRegex` Typ se používá pro porovnávání regulárního výrazu. `IsLengthRange` Typ přijímá minimální a maximální délku řetězce. Přidejte element **predikáty** do prvku **BuildingBlocks** , pokud neexistuje s následujícími prvky predikátu:

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

5. Každý element **InputValidation** je vytvořen pomocí definovaných prvků **predikátu** . Tento prvek umožňuje provádět logické agregace, které jsou podobné `and` a. `or` Přidejte element **InputValidations** do elementu **BuildingBlocks** , pokud neexistuje s následujícím elementem **InputValidation** :

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

6. Ujistěte se, že technický profil **PolicyProfile** obsahuje následující prvky:

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

## <a name="test-your-policy"></a>Testování zásad

Při testování aplikací v Azure AD B2C může být užitečné, aby byl token Azure AD B2C vrácen `https://jwt.ms` , aby bylo možné zkontrolovat deklarace identity v ní.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
5. Na stránce vlastní zásady klikněte na **nahrát zásadu**.
6. Vyberte **přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *SingUpOrSignInPasswordComplexity. XML* .
7. Klikněte na **Odeslat**.

### <a name="run-the-policy"></a>Spustit zásady

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_signup_signin_password_complexity*.
2. V případě **aplikace**vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pro zobrazení tokenu by se měla zobrazit `https://jwt.ms` **Adresa URL odpovědi** .
3. Klikněte na **Spustit**.
4. Vyberte **zaregistrovat se hned**, zadejte e-mailovou adresu a zadejte nové heslo. Doprovodné materiály jsou uvedeny na základě omezení hesel. Dokončete zadávání informací o uživateli a pak klikněte na **vytvořit**. Měl by se zobrazit obsah vráceného tokenu.

## <a name="next-steps"></a>Další postup

- Naučte se [Konfigurovat změnu hesla pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


