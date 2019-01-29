---
title: Účtu na sociální síti deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: Účtu na sociální síti deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9fab5394fadc029b9415370c6bc8c0a3a3642054
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156696"
---
# <a name="social-accounts-claims-transformations"></a>Transformace deklarací identity účtů na sociálních sítích

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory (Azure AD) B2C, se ukládají identity účtu na sociální síti v `userIdentities` atribut **alternativeSecurityIdCollection** typ deklarace identity. Každá položka v **alternativeSecurityIdCollection** určuje vystavitele (název zprostředkovatele identit, jako je například facebook.com) a `issuerUserId`, který je jedinečný identifikátor uživatele pro vydavatele. 

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

Tento článek obsahuje příklady použití transformace deklarací identity účtu na sociální síti schématu architekturu rozhraní identit v Azure AD B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Vytvoří reprezentaci JSON alternativeSecurityId vlastnosti uživatele, který lze použít ve volání do služby Azure Active Directory. Další informace najdete v tématu [AlternativeSecurityId od schématu](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType).

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | řetězec | Typ ClaimType určující jedinečný uživatelský identifikátor používá zprostředkovatel identity v sociálních sítích. |
| InputClaim | identityProvider | řetězec | Typ ClaimType, který určuje název zprostředkovatele identity účtu na sociální síti, jako je například facebook.com. |
| outputClaim | alternativeSecurityId | řetězec | Typ ClaimType, který je vytvořen po zavolání ClaimsTransformation. Obsahuje informace o identitě uživatele účtu na sociální síti. **Vystavitele** je hodnota `identityProvider` deklarací identity. **IssuerUserId** je hodnota `key` deklarace identity ve formátu base64. |

Použijte Tato transformace ke generování deklarací identity `alternativeSecurityId` typu deklarace identity. Používá se všechny identity v sociálních sítích zprostředkovatele technické profily, jako například `Facebook-OAUTH`. ID uživatele účtu na sociální síti a název zprostředkovatele identit obdrží následující transformace deklarací identity. Výstup této technickém profilu je řetězec formátu JSON, který lze použít v adresáři služby Azure AD.  

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **Klíč**: 12334
    - **identityProvider**: Facebook.com
- Výstupní deklarace identit:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Přidá `AlternativeSecurityId` do `alternativeSecurityIdCollection` deklarací identity. 

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Položka | řetězec | Přidat do výstupu deklarace typu deklarace identity. |
| InputClaim | kolekce | alternativeSecurityIdCollection | ClaimTypes, které jsou používány transformace deklarací identity, pokud je k dispozici v zásadách. Pokud je zadán, přidá transformace deklarací identity `item` na konec kolekce. |
| outputClaim | kolekce | alternativeSecurityIdCollection | ClaimTypes vytvořený po zavolání této ClaimsTransformation. Nová kolekce, která obsahuje položky ze vstupu `collection` a `item`. |

Následující příklad propojí nové sociálních identit pomocí existujícího účtu. Chcete-li propojit nový sociálních identit: 
1. V **AAD UserReadUsingAlternativeSecurityId** a **AAD UserReadUsingObjectId** technické profily výstup uživatele **alternativeSecurityIds** deklarací identity.
1. Požádejte uživatele, aby se přihlaste jedním zprostředkovatelů identity, které nejsou přiřazeny k tomuto uživateli. 
1. Použití **CreateAlternativeSecurityId** deklarace identity transformace, vytvořte nový **alternativeSecurityId** typ s názvem deklarace identity `AlternativeSecurityId2` 
1. Volání **AddItemToAlternativeSecurityIdCollection** přidat transformace deklarací identity **AlternativeSecurityId2** deklaraci identity pro existující **AlternativeSecurityIds** deklarace identity. 
1. Zachovat **alternativeSecurityIds** deklaraci identity pro uživatelský účet

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **kolekce**: [{"vystavitele": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Výstupní deklarace identit:
    - **kolekce**: [{"vystavitele": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Vrátí seznam vystavitelů z **alternativeSecurityIdCollection** deklarace identity do nového **třída stringCollection** deklarací identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Typ ClaimType se použije k získání seznamu zprostředkovatelů identity (issuer). |
| outputClaim | identityProvidersCollection | stringCollection | ClaimTypes vytvořený po zavolání této ClaimsTransformation. Seznam zprostředkovatelů identit přidružit alternativeSecurityIdCollection vstupní deklaraci identity |

Následující transformace deklarací identity přečte uživatel **alternativeSecurityIds** deklarace identity a extrahuje seznam názvů poskytovatele identity přidružené k tomuto účtu. Použití výstupu **identityProvidersCollection** uživateli zobrazit seznam poskytovatelů identity přidružené k účtu. Nebo na stránce výběru zprostředkovatele identity, filtrovat seznam poskytovatelů identity výstupu **identityProvidersCollection** deklarací identity. Ano může uživatel vybrat propojení nového sociálních identit, který ještě není přidružená k účtu. 

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

- Vstupní deklarace identity:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU ="}]
- Výstupní deklarace identit:
    - **identityProvidersCollection**: ["facebook.com", "google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Odebere **AlternativeSecurityId** ze **alternativeSecurityIdCollection** deklarací identity. 

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | řetězec | Typ ClaimType, který obsahuje název zprostředkovatele identit, která má být odebrán z kolekce. |
| InputClaim | kolekce | alternativeSecurityIdCollection | ClaimTypes, které používají deklarace identity transformace. Transformace deklarací identity identityProvider odebere z kolekce. |
| outputClaim | kolekce | alternativeSecurityIdCollection | ClaimTypes vytvořený po zavolání této ClaimsTransformation. Nová kolekce, po identityProvider odebrán z kolekce. |

Následující příklad zruší propojení mezi sociálních identit pomocí existujícího účtu. Chcete-li odpojit sociálních identit: 
1. V **AAD UserReadUsingAlternativeSecurityId** a **AAD UserReadUsingObjectId** technické profily výstup uživatele **alternativeSecurityIds** deklarací identity.
2. Požádejte uživatele o výběru účtu odebrat ze seznamu zprostředkovatelů identity, které jsou spojeny s tímto uživatelem, který sociálních sítí. 
3. Volání technický profil deklarace identity transformace, která volá **RemoveAlternativeSecurityIdByIdentityProvider** transformace, který odebrat vybrané sociálních identit, název zprostředkovatele identit pomocí deklarací identity.
4. Zachovat **alternativeSecurityIds** deklaraci identity pro uživatelský účet.

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **identityProvider**: facebook.com
    - **kolekce**: [{"vystavitele": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU ="}]
- Výstupní deklarace identit:
    - **kolekce**: [{"vystavitele": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
