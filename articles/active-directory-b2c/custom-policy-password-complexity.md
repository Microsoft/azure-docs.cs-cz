---
title: Konfigurace složitosti hesla pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Jak nakonfigurovat požadavky na složitost hesla pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388999"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace složitosti hesla pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory B2C (Azure AD B2C) můžete nakonfigurovat požadavky na složitost pro hesla, která jsou k dispozici uživatelem při vytváření účtu. Ve výchozím nastavení používá Azure AD B2C používání **silných** hesel. V tomto článku se dozvíte, jak nakonfigurovat složitost hesla ve [vlastních zásadách](custom-policy-overview.md). Je také možné nakonfigurovat složitost hesla v [uživatelských tocích](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlašování pomocí místních účtů.


## <a name="add-the-elements"></a>Přidat elementy

Pokud chcete nakonfigurovat složitost hesla, přepište `newPassword` `reenterPassword` [typy deklarací](claimsschema.md) a a odkaz na [ověření predikátu](predicates.md#predicatevalidations). Element PredicateValidations seskupuje sadu predikátů pro vytvoření ověřování vstupu uživatele, které lze použít na typ deklarace identity. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Přidejte `newPassword` deklarace a `reenterPassword` k elementu **ClaimsSchema** .

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikáty](predicates.md) definují základní ověření pro kontrolu hodnoty typu deklarace identity a vrátí hodnotu true nebo false. Ověřování se provádí pomocí zadaného elementu metody a sady parametrů relevantních pro metodu. Do prvku **BuildingBlocks** přidejte následující predikáty hned po ukončení `</ClaimsSchema>` elementu:

    ```xml
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

1. Do elementu **BuildingBlocks** přidejte následující ověřování predikátu hned po ukončení `</Predicates>` elementu:

    ```xml
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

1. Následující technické profily jsou [technické profily služby Active Directory](active-directory-technical-profile.md), které čtou a zapisují data do Azure Active Directory. Tyto technické profily popište v souboru rozšíření. Použijte `PersistedClaims` k zakázání zásad silného hesla. Vyhledejte element **ClaimsProviders** .  Přidejte následující zprostředkovatele deklarací identity následujícím způsobem:

    ```xml
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

## <a name="test-your-policy"></a>Testování zásad

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
5. Na stránce vlastní zásady klikněte na **nahrát zásadu**.
6. Vyberte **přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
7. Klikněte na **Odeslat**.

### <a name="run-the-policy"></a>Spustit zásadu

1. Otevřete zásadu registrace nebo přihlašování. Například *B2C_1A_signup_signin*.
2. V případě **aplikace**vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pro zobrazení tokenu by se měla zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Klikněte na **Spustit**.
4. Vyberte **zaregistrovat se hned**, zadejte e-mailovou adresu a zadejte nové heslo. Doprovodné materiály jsou uvedeny na základě omezení hesel. Dokončete zadávání informací o uživateli a pak klikněte na **vytvořit**. Měl by se zobrazit obsah vráceného tokenu.

## <a name="next-steps"></a>Další kroky

- Naučte se [Konfigurovat změnu hesla pomocí vlastních zásad v Azure Active Directory B2C](custom-policy-password-change.md).
- Přečtěte si další informace o [predikátech](predicates.md) a elementech [PredicateValidations](predicates.md#predicatevalidations) v odkazu na IEF.
