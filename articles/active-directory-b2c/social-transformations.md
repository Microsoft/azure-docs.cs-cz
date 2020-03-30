---
title: Sociální účet nároky transformace příklady pro vlastní zásady
titleSuffix: Azure AD B2C
description: Účet na sociální chod nároky příklady transformace pro rozhraní Identity Experience Framework (IEF) schéma Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb713651aca266ab2546ff26c3cd0175a4cbc289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183750"
---
# <a name="social-accounts-claims-transformations"></a>Sociální účty tvrdí, transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ve službě Azure Active Directory B2C (Azure AD B2C) jsou identity účtů na sociálních sítích uloženy v atributu `userIdentities` alternativního typu deklarace **identity SecurityIdCollection.** Každá položka v **alternativeSecurityIdCollection** určuje vystavitele (název zprostředkovatele identity, `issuerUserId`například facebook.com) a , což je jedinečný identifikátor uživatele pro vystavitele.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Tento článek obsahuje příklady pro použití sociálního účtu deklarace transformace schématu identity experience framework v Azure AD B2C. Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>Vytvořit možnost Vytvořit alternativní soubor Zabezpečení

Vytvoří json reprezentaci uživatele alternativeSecurityId vlastnost, která lze použít v volání služby Azure Active Directory. Další informace naleznete v tématu [Schéma AlternativeSecurityId.](https://docs.microsoft.com/graph/api/resources/alternativesecurityid)

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | key | řetězec | ClaimType, který určuje jedinečný identifikátor uživatele používaný poskytovatelem sociální identity. |
| Vstupní deklarace | identityProvider | řetězec | ClaimType, který určuje název poskytovatele identity účtu sociální sítě, například facebook.com. |
| Výstupní nárok | alternativeSecurityId | řetězec | ClaimType, který je vytvořen po ClaimsTransformation byla vyvolána. Obsahuje informace o identitě uživatele účtu na sociální síti. **Emitent** je hodnota deklarace. `identityProvider` Číslo **issuerUserId** je hodnota `key` deklarace ve formátu base64. |

Pomocí této transformace deklarací generovat `alternativeSecurityId` ClaimType. Používá se všemi technickými profily poskytovatele `Facebook-OAUTH`sociální identity, například . Následující transformace deklarací identity obdrží ID uživatelského účtu a název zprostředkovatele identity. Výstup tohoto technického profilu je formát řetězce JSON, který lze použít v adresářových službách Azure AD.

```XML
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

- Vstupní deklarace:
    - **klíč**: 12334
    - **identityProvider**: Facebook.com
- Výstupní nároky:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Přidá `AlternativeSecurityId` `alternativeSecurityIdCollection` deklaraci.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | položka | řetězec | ClaimType, které mají být přidány do výstupní deklarace. |
| Vstupní deklarace |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou používány transformace deklarací, pokud jsou k dispozici v zásadách. Pokud je k dispozici, `item` deklarace transformace přidá na konci kolekce. |
| Výstupní nárok |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou vytvořeny po této ClaimsTransformation byla vyvolána. Nová kolekce, která obsahuje položky ze vstupní `collection` a `item`. |

Následující příklad propojuje novou sociální identitu s existujícím účtem. Propojení nové sociální identity:
1. V **aad-userReadUsingAlternativeSecurityId** a **AAD-UserReadUsingObjectId** technické profily, výstup **uživatele alternativeSecurityIds** deklarace.
1. Požádejte uživatele, aby se přihlásil pomocí jednoho z poskytovatelů identity, kteří nejsou přidruženi k tomuto uživateli.
1. Pomocí **transformace deklarace identity CreateAlternativeSecurityId** vytvořte nový **typ deklarace zabezpečení s** názvem`AlternativeSecurityId2`
1. Volání **AddItemToAlternativeSecurityIdCollection** deklarace transformace přidat **AlternativeSecurityId2** deklarace existující **AlternativeSecurityIds** deklarace.
1. Zachovat **alternativní SecurityIds** nárok na uživatelský účet

```XML
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

- Vstupní deklarace:
    - **položka**: { "emitent": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **kolekce**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Výstupní nároky:
    - **kolekce**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "emitent": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Vrátí seznam vystavitelů z **alternativeSecurityIdCollection** deklarace do nové **deklarace stringCollection.**

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | alternativeSecurityIdCollection | alternativeSecurityIdCollection | ClaimType, které mají být použity k získání seznamu zprostředkovatelů identity (vystavit). |
| Výstupní nárok | identityProvidersCollection | Stringcollection | ClaimTypes, které jsou vytvořeny po této ClaimsTransformation byla vyvolána. Seznam zprostředkovatelů identit přidružit k alternativníSecurityIdCollection vstupní deklarace |

Následující transformace deklarací identity přečte **deklaraci identity alternativu zabezpečení** a extrahuje seznam názvů zprostředkovatelů identity přidružených k tomuto účtu. Pomocí výstupní **identityProvidersCollection** zobrazíte uživateli seznam zprostředkovatelů identity přidružených k účtu. Nebo na stránce výběru zprostředkovatele identity filtrovat seznam zprostředkovatelů identity na základě deklarace **identityProvidersCollection** výstupu. Uživatel tak může vybrat propojení nové sociální identity, která ještě není přidružena k účtu.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Vstupní deklarace:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" ]
- Výstupní nároky:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>Odebrat alternativu SecurityIdByIdentityProvider

Odebere **AlternativeSecurityId** z **alternativeSecurityIdCollection** deklarace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | identityProvider | řetězec | ClaimType, který obsahuje název zprostředkovatele identity, který má být odebrán z kolekce. |
| Vstupní deklarace |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou používány transformace deklarací. Deklarace identity transformace odebere identityProvider z kolekce. |
| Výstupní nárok |  – kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou vytvořeny po této ClaimsTransformation byla vyvolána. Nová kolekce po identityProvider odebránz kolekce. |

Následující příklad odpojí jednu ze sociálníidentity s existujícím účtem. Zrušení propojení sociální identity:
1. V **aad-userReadUsingAlternativeSecurityId** a **AAD-UserReadUsingObjectId** technické profily, výstup **uživatele alternativeSecurityIds** deklarace.
2. Požádejte uživatele, aby vybral, který účet na sociální síti má být odebrán z poskytovatelů identit seznamu, kteří jsou přidruženi k tomuto uživateli.
3. Volání technického profilu transformace deklarací, který volá **transformaci RemoveAlternativeSecurityIdByIdentityProvider,** která odebrala vybranou sociální identitu pomocí názvu zprostředkovatele identity.
4. Zachovat **alternativníSecurityIds** nárok na uživatelský účet.

```XML
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

- Vstupní deklarace:
    - **identityProvider**: facebook.com
    - **kolekce**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "emitent": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Výstupní nároky:
    - **kolekce**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
