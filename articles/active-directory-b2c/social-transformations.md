---
title: Příklady transformace deklarací účtu sociálních sítí pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady transformace deklarací účtu sociálních sítí pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953113"
---
# <a name="social-accounts-claims-transformations"></a>Transformace deklarací účtů sociálních sítí

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory B2C (Azure AD B2C) jsou identity sociálního účtu uložené v `userIdentities` atributu typu deklarace identity **alternativeSecurityIdCollection** . Každá položka v **alternativeSecurityIdCollection** určuje vystavitele (název zprostředkovatele identity, jako je Facebook.com) a `issuerUserId` , který je jedinečným identifikátorem uživatele pro vystavitele.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Tento článek popisuje příklady použití transformací v rámci schématu rozhraní identity v účtu sociální sítě v Azure AD B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Vytvoří reprezentaci JSON vlastnosti alternativeSecurityId uživatele, kterou lze použít v voláních Azure Active Directory. Další informace najdete v tématu schéma [AlternativeSecurityId](/graph/api/resources/alternativesecurityid) .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | řetězec | Deklarace identity, která určuje jedinečný identifikátor uživatele používaný poskytovatelem sociální identity. |
| InputClaim | identityProvider | řetězec | Deklarace ClaimType, která určuje název zprostředkovatele identity účtu sociální sítě, například facebook.com. |
| OutputClaim | alternativeSecurityId | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání ClaimsTransformation. Obsahuje informace o identitě uživatele účtu sociální sítě. **Vystavitel** je hodnota `identityProvider` deklarace identity. **IssuerUserId** je hodnota `key` deklarace identity ve formátu base64. |

Tuto transformaci deklarací identity použijte k vygenerování `alternativeSecurityId` deklarace ClaimType. Používá se u všech technických profilů zprostředkovatele sociální identity, jako je například `Facebook-OAUTH` . Následující transformace deklarací identity obdrží ID účtu sociální sítě a název zprostředkovatele identity. Výstupem tohoto technického profilu je formát řetězce JSON, který se dá použít v adresářových službách Azure AD.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **klíč**: 12334
    - **identityProvider**: Facebook.com
- Deklarace výstupů:
    - **alternativeSecurityId**: {"Issuer": "Facebook.com"; "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Přidá `AlternativeSecurityId` k `alternativeSecurityIdCollection` deklaraci identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | položka | řetězec | Deklarace ClaimType, která se má přidat do výstupní deklarace |
| InputClaim |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou používány transformací deklarací identity, pokud jsou v zásadě k dispozici. Je-li tento příkaz zadán, transformace deklarací přidá na `item` konec kolekce. |
| OutputClaim |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation. Nová kolekce, která obsahuje položky ze vstupu `collection` a `item` . |

Následující příklad propojuje novou sociální identitu s existujícím účtem. Propojení nové sociální identity:
1. V technických profilech **AAD-UserReadUsingAlternativeSecurityId** a **AAD-UserReadUsingObjectId** vypíše uživatel deklaraci identity **alternativeSecurityIds** uživatele.
1. Požádejte uživatele, aby se přihlásil pomocí některého z poskytovatelů identity, kteří nejsou přidružení k tomuto uživateli.
1. Pomocí transformace deklarací identity **CreateAlternativeSecurityId** vytvořte nový typ deklarace identity **alternativeSecurityId** s názvem. `AlternativeSecurityId2`
1. Zavolejte transformaci deklarací **AddItemToAlternativeSecurityIdCollection** a přidejte tak deklaraci identity **AlternativeSecurityId2** do existující deklarace identity **AlternativeSecurityIds** .
1. Zachovat deklaraci identity **alternativeSecurityIds** pro uživatelský účet

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **položka**: {"Issuer": "Facebook.com", "issuerUserId": "MTIzNDU ="}
    - **kolekce**: [{"Issuer": "Live.com"; "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Deklarace výstupů:
    - **kolekce**: [{"Issuer": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}; {"Issuer": "Facebook.com", "issuerUserId": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Vrátí seznam vystavitelů z deklarace **alternativeSecurityIdCollection** do nové deklarace identitycollection **řetězce** .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Deklarace identity, která se má použít k získání seznamu zprostředkovatelů identity (Issuer) |
| OutputClaim | identityProvidersCollection | Třída StringCollection | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation. Seznam zprostředkovatelů identity přidružit ke vstupní deklaraci alternativeSecurityIdCollection |

Následující transformace deklarací identity přečte deklaraci identity uživatele **alternativeSecurityIds** a extrahuje seznam názvů zprostředkovatelů identity přidružených k tomuto účtu. Pomocí výstupního **identityProvidersCollectionu** můžete zobrazit uživatele seznam zprostředkovatelů identity přidružených k tomuto účtu. Nebo na stránce Výběr zprostředkovatele identity vyfiltrujte seznam zprostředkovatelů identity na základě výstupní deklarace identity **identityProvidersCollection** . Uživatel tak může vybrat propojení nové sociální identity, která ještě není přidružená k účtu.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace identity:
    - **alternativeSecurityIdCollection**: [{"Issuer": "Google.com"; "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}; {"Issuer": "Facebook.com"; "issuerUserId": "MTIzNDU ="}]
- Deklarace výstupů:
    - **identityProvidersCollection**: ["Facebook.com"; "Google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Odebere **AlternativeSecurityId** z deklarace **alternativeSecurityIdCollection** .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | řetězec | Deklarace ClaimType obsahující název zprostředkovatele identity, který se má odebrat z kolekce. |
| InputClaim |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou používány transformací deklarací. Transformace deklarací odstraní identityProvider z kolekce. |
| OutputClaim |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation. Nová kolekce po odebrání identityProvider z kolekce. |

Následující příklad odpojí jednu ze sociálních identit s existujícím účtem. Odpojení sociální identity:
1. V technických profilech **AAD-UserReadUsingAlternativeSecurityId** a **AAD-UserReadUsingObjectId** vypíše uživatel deklaraci identity **alternativeSecurityIds** uživatele.
2. Požádejte uživatele, aby vybral účet pro sociální sítě, který se má odebrat, ze seznamu zprostředkovatelů identity, kteří jsou přidruženi k tomuto uživateli.
3. Zavolejte na technický profil transformace deklarací identity, který volá transformaci deklarací **RemoveAlternativeSecurityIdByIdentityProvider** , která odebrala vybranou sociální identitu pomocí názvu zprostředkovatele identity.
4. Zachovat deklaraci identity **alternativeSecurityIds** pro uživatelský účet.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **identityProvider**: Facebook.com
    - **kolekce**: [{"Issuer": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}; {"Issuer": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Deklarace výstupů:
    - **kolekce**: [{"Issuer": "Live.com"; "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]