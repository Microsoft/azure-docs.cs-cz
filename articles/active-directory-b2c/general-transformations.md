---
title: Příklady obecné transformace deklarací identity pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady obecných transformačních deklarací identity pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d9730168764f0ba683a246f9ac224c13d3bf31
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982802"
---
# <a name="general-claims-transformations"></a>Obecné transformace deklarací identity

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití obecných transformací identity schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Kopírovat hodnotu deklarace identity na jinou. Obě deklarace identity musí být ze stejného typu.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | řetězec, int | Typ deklarace, který se má zkopírovat |
| OutputClaim | OutputClaim | řetězec, int | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

Tuto transformaci deklarací identity použijte ke zkopírování hodnoty z řetězce nebo číselné deklarace na jinou deklaraci identity. V následujícím příkladu se zkopíruje hodnota deklarace identity externalEmail na deklaraci identity e-mailu.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: bob@contoso.com
- Deklarace výstupů:
    - **outputClaim**: bob@contoso.com 

## <a name="doesclaimexist"></a>DoesClaimExist

Kontroluje, zda **inputClaim** existuje nebo není, a nastaví **outputClaim** na hodnotu true nebo false.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Všechny | Vstupní deklarace identity, jejíž existence je potřeba ověřit. |
| OutputClaim | OutputClaim | Boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

Pomocí této transformace deklarací identity ověřte, zda deklarace identity existuje nebo obsahuje libovolnou hodnotu. Vrácená hodnota je logická hodnota, která označuje, zda existuje deklarace identity. Následující příklad zkontroluje, jestli tato e-mailová adresa existuje.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
  - **inputClaim**: someone@contoso.com
- Deklarace výstupů:
  - **outputClaim**: true

## <a name="hash"></a>Hodnota hash

Vytvoří hodnotu hash zadaného prostého textu pomocí soli a tajného klíče. Použitý algoritmus hash je SHA-256.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | prostý | string | Vstupní deklarace identity, která se má zašifrovat |
| InputClaim | sůl | string | Parametr Salt. Náhodnou hodnotu můžete vytvořit pomocí `CreateRandomString` transformace deklarací identity. |
| InputParameter | randomizerSecret | string | Odkazuje na existující **klíč zásad**Azure AD B2C. Pokud chcete vytvořit nový klíč zásad: v tenantovi Azure AD B2C v části **Spravovat**vyberte **Architektura prostředí identity**. Vyberte **klíče zásad** pro zobrazení klíčů, které jsou k dispozici ve vašem tenantovi. Vyberte **Přidat**. V případě **možností**vyberte možnost **ručně**. Zadejte název (předponu *B2C_1A_* možné přidat automaticky.) Do textového pole **tajný kód** zadejte libovolný tajný klíč, který chcete použít, například 1234567890. V případě **použití klíče**vyberte možnost **podpis**. Vyberte **Vytvořit**. |
| OutputClaim | kontrole | string | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. Deklarace identity konfigurovaná ve `plaintext` inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
  - **prostý text**: MyPass@word1
  - **sůl**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Deklarace výstupů:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =
