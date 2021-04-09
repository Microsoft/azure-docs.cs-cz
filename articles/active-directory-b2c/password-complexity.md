---
title: Konfigurace požadavků na složitost hesla
titleSuffix: Azure AD B2C
description: Jak nakonfigurovat požadavky na složitost pro hesla dodaná příjemci v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 81c6e58e34f30d5736c40c77a308321dee28ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224261"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurace požadavků na složitost pro hesla v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) podporuje změnu požadavků na složitost hesel zadaných koncovým uživatelem při vytváření účtu. Ve výchozím nastavení používá Azure AD B2C používání **silných** hesel. Azure AD B2C podporuje také možnosti konfigurace pro kontrolu složitosti hesel, která zákazníci můžou používat.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Vynucení pravidla hesla

Při registraci nebo resetování hesla musí koncový uživatel dodat heslo, které splňuje pravidla složitosti. Pravidla složitosti hesla jsou vynutila pro jednotlivé uživatelské toky. Je možné, aby jeden uživatelský tok při registraci vyžadoval čtyřmístný PIN kód, zatímco jiný tok uživatele během registrace vyžaduje osm znaků řetězce. Například můžete použít tok uživatele s odlišnou složitostí hesla pro dospělé než pro děti.

Složitost hesla se během přihlašování nikdy vynutila. Uživatelům se během přihlašování nikdy nevyzve ke změně hesla, protože nesplňuje aktuální požadavek na složitost.

Složitost hesla se dá nakonfigurovat v následujících typech uživatelských toků:

- Tok uživatelů registrace nebo přihlašování
- Tok uživatele pro resetování hesla

Pokud používáte vlastní zásady, můžete ([nakonfigurovat složitost hesla ve vlastních zásadách](password-complexity.md)).

## <a name="configure-password-complexity"></a>Konfigurace složitosti hesla

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů**.
2. Vyberte tok uživatele a klikněte na **vlastnosti**.
3. V části **složitost hesla** změňte složitost hesla pro tento tok uživatele na **jednoduché**, **silné** nebo **vlastní**.

### <a name="comparison-chart"></a>Srovnávací graf

| Složitost | Description |
| --- | --- |
| Jednoduchost | Heslo, které má alespoň 8 až 64 znaků. |
| Silná | Heslo, které má alespoň 8 až 64 znaků. Vyžaduje 3 ze 4 malých a velkých písmen, číslic a symbolů. |
| Vlastní | Tato možnost poskytuje nejvyšší kontrolu nad pravidly složitosti hesla.  Umožňuje nakonfigurovat vlastní délku.  Umožňuje taky přijímat jenom hesla (PIN kód). |

## <a name="custom-options"></a>Vlastní možnosti

### <a name="character-set"></a>Znaková sada

Slouží k přijetí pouze číslic (PIN) nebo plné znakové sady.

- Při zadávání hesla se **čísly jenom** povolují jenom číslice (0-9).
- **Vše** umožňuje libovolné písmeno, číslici nebo symbol.

### <a name="length"></a>Délka

Umožňuje řídit požadavky na délku hesla.

- **Minimální délka** musí být aspoň 4.
- **Maximální délka** musí být větší nebo rovna minimální délce a maximálně může být 256 znaků.

### <a name="character-classes"></a>Třídy znaků

Umožňuje řídit různé typy znaků používané v hesle.

- **2 ze 4: malý znak, velké písmeno, číslo (0-9), symbol zajistí,** že heslo obsahuje alespoň dva typy znaků. Například číslo a malý znak.
- **3 ze 4: malý znak, Velká písmena, číslo (0-9), symbol zajistí,** že heslo obsahuje alespoň tři typy znaků. Například číslo, malý znak a znak velkého písmene.
- **4 ze 4: malá písmena, Velká písmena, číslo (0-9), symbol zajistí,** že heslo obsahuje všechny typy znaků.

    > [!NOTE]
    > Výsledkem **4 ze 4** může být frustrace koncového uživatele. Některé studie ukázaly, že tento požadavek nevylepšuje entropii hesla. Viz [pokyny k heslům pro NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) .

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Ověření predikátu hesla

Pokud chcete nakonfigurovat složitost hesla, přepište `newPassword` `reenterPassword` [typy deklarací](claimsschema.md) a a odkaz na [ověření predikátu](predicates.md#predicatevalidations). Element PredicateValidations seskupuje sadu predikátů pro vytvoření ověřování vstupu uživatele, které lze použít na typ deklarace identity. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Přidejte `newPassword` deklarace a `reenterPassword` k elementu **ClaimsSchema** .

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. [Predikáty](predicates.md) definují základní ověření pro kontrolu hodnoty typu deklarace identity a vrátí hodnotu true nebo false. Ověřování se provádí pomocí zadaného elementu metody a sady parametrů relevantních pro metodu. Do prvku **BuildingBlocks** přidejte následující predikáty hned po ukončení `</ClaimsSchema>` elementu:

    ```xml
    <!-- 
    <BuildingBlocks>-->
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
    <!-- 
    </BuildingBlocks>-->
    ```

1. Do elementu **BuildingBlocks** přidejte následující ověřování predikátu hned po ukončení `</Predicates>` elementu:

    ```xml
    <!-- 
    <BuildingBlocks>-->
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
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Zakázat silné heslo 

Následující technické profily jsou [technické profily služby Active Directory](active-directory-technical-profile.md), které čtou a zapisují data do Azure Active Directory. Tyto technické profily popište v souboru rozšíření. Použijte `PersistedClaims` k zakázání zásad silného hesla. Vyhledejte element **ClaimsProviders** .  Přidejte následující zprostředkovatele deklarací identity následujícím způsobem:

```xml
<!-- 
<ClaimsProviders>-->
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
<!-- 
</ClaimsProviders>-->
```

Pokud používáte zásady [přihlašování založené na uživatelských jménech](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) , aktualizujte `AAD-UserWriteUsingLogonEmail` , `AAD-UserWritePasswordUsingObjectId` a `LocalAccountWritePasswordUsingObjectId` technické profily pomocí zásad *DisableStrongPassword* .

Uložte soubor zásad.

## <a name="test-your-policy"></a>Testování zásad

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
5. Na stránce vlastní zásady klikněte na **nahrát zásadu**.
6. Vyberte **přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
7. Klikněte na **Odeslat**.

### <a name="run-the-policy"></a>Spustit zásadu

1. Otevřete zásadu registrace nebo přihlašování. Například *B2C_1A_signup_signin*.
2. V případě **aplikace** vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pro zobrazení tokenu by se měla zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Klikněte na **Spustit**.
4. Vyberte **zaregistrovat se hned**, zadejte e-mailovou adresu a zadejte nové heslo. Doprovodné materiály jsou uvedeny na základě omezení hesel. Dokončete zadávání informací o uživateli a pak klikněte na **vytvořit**. Měl by se zobrazit obsah vráceného tokenu.

## <a name="next-steps"></a>Další kroky

- Naučte se [Konfigurovat změnu hesla v Azure Active Directory B2C](add-password-change-policy.md).
- Přečtěte si další informace o [predikátech](predicates.md) a elementech [PredicateValidations](predicates.md#predicatevalidations) v odkazu na IEF.


::: zone-end
