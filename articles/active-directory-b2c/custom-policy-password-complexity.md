---
title: Konfigurace složitosti hesla pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Jak nakonfigurovat požadavky na složitost hesla pomocí vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138430"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace složitosti hesla pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ve službě Azure Active Directory B2C (Azure AD B2C) můžete nakonfigurovat požadavky na složitost hesel, které poskytuje uživatel při vytváření účtu. Ve výchozím nastavení Azure AD B2C používá **silná** hesla. Tento článek ukazuje, jak nakonfigurovat složitost hesla ve [vlastních zásadách](custom-policy-overview.md). Je také možné nakonfigurovat složitost hesla v [tocích uživatelů](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md). Měli byste mít funkční vlastní zásady pro registraci a přihlášení pomocí místních účtů.


## <a name="add-the-elements"></a>Přidejte prvky

Chcete-li nakonfigurovat složitost hesla, přepište `newPassword` typy `reenterPassword` [deklarací identity](claimsschema.md) s odkazem na [ověření predikátu](predicates.md#predicatevalidations). PredicateValidations element skupiny sadu predikáty tvořit ověření vstupu uživatele, které lze použít pro typ deklarace. Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.

1. Vyhledejte element [BuildingBlocks.](buildingblocks.md) Pokud prvek neexistuje, přidejte jej.
1. Vyhledejte [ClaimsSchema](claimsschema.md) element. Pokud prvek neexistuje, přidejte jej.
1. Přidejte `newPassword` `reenterPassword` a deklarace **identity claimsSchema** element.

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikáty](predicates.md) definuje základní ověření pro kontrolu hodnoty typu deklarace a vrátí true nebo false. Ověření se provádí pomocí zadaného prvku metody a sadu parametrů relevantních pro metodu. Přidejte následující predikáty do prvku **BuildingBlocks,** bezprostředně `</ClaimsSchema>` po uzavření prvku:

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Přidejte následující predikát ověření **buildingblocks** prvek, bezprostředně po `</Predicates>` uzavření prvku:

    ```XML
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. Následující technické profily jsou [technické profily služby Active Directory](active-directory-technical-profile.md), které přijímají a zapisují data do služby Azure Active Directory. Přepište tyto technické profily v souboru rozšíření. Slouží `PersistedClaims` k zakázání zásad silného hesla. Najít **ClaimsProviders** element.  Přidejte následující zprostředkovatele deklarací následujícím způsobem:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Uložte soubor zásad.

## <a name="test-your-policy"></a>Otestujte své zásady

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **rozhraní Identity Experience Framework**.
5. Na stránce Vlastní zásady klikněte na **Nahrát zásady**.
6. Vyberte **Přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml.*
7. Klikněte na **Odeslat**.

### <a name="run-the-policy"></a>Spuštění zásady

1. Otevřete zásady registrace nebo přihlášení. Například *B2C_1A_signup_signin*.
2. V **aplikaci**vyberte aplikaci, kterou jste dříve zaregistrovali. Chcete-li zobrazit **Reply URL** token, měla `https://jwt.ms`by se zobrazit adresa URL odpovědi .
3. Klikněte na **Spustit**.
4. Vyberte **Zaregistrovat se ,** zadejte e-mailovou adresu a zadejte nové heslo. Pokyny k omezení hesla. Dokončete zadávání informací o uživateli a klepněte na tlačítko **Vytvořit**. Měli byste vidět obsah tokenu, který byl vrácen.

## <a name="next-steps"></a>Další kroky

- Přečtěte [si, jak nakonfigurovat změnu hesla pomocí vlastních zásad ve službě Azure Active Directory B2C](custom-policy-password-change.md).
- Další informace o [predikáty](predicates.md) a [PredicateValidations prvky](predicates.md#predicatevalidations) v odkazu IEF.
