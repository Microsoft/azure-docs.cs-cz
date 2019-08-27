---
title: Příklady základních transformací deklarací identit pro schéma rozhraní Azure Active Directory B2C prostředí identity
description: Příklady základních transformací deklarací identity pro schéma rozhraní Azure Active Directory B2C prostředí identit.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cea33cb61f8f8d0fe305a757f11c80bc5da24ca
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032903"
---
# <a name="general-claims-transformations"></a>Obecné transformace deklarací identity

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití obecných transformací identity schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Kontroluje, zda **inputClaim** existuje nebo není, a nastaví **outputClaim** na hodnotu true nebo false.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Any | Vstupní deklarace identity, jejíž existence je potřeba ověřit. |
| OutputClaim | outputClaim | boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputClaim**:someone@contoso.com
- Deklarace výstupů:
  - **outputClaim**: true

## <a name="hash"></a>Hash

Vytvoří hodnotu hash zadaného prostého textu pomocí soli a tajného klíče. Použitý algoritmus hash je SHA-256.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | prostý | řetězec | Vstupní deklarace identity, která se má zašifrovat |
| InputClaim | sůl | řetězec | Parametr Salt. Pomocí `CreateRandomString` transformace deklarací identity můžete vytvořit náhodnou hodnotu. |
| InputParameter | randomizerSecret | řetězec | Odkazuje na existující **klíč zásad**Azure AD B2C. Vytvoření nového klíče zásad: Ve vašem tenantovi Azure AD B2C v části **Spravovat**vyberte **Architektura prostředí identity**. Vyberte **klíče zásad** pro zobrazení klíčů, které jsou k dispozici ve vašem tenantovi. Vyberte **Přidat**. V případě **možností**vyberte možnost **ručně**. Zadejte název (předponu *B2C_1A_* je možné přidat automaticky.) Do textového pole **tajný kód** zadejte libovolný tajný klíč, který chcete použít, například 1234567890. V případě **použití klíče**vyberte možnost **podpis**. Vyberte **Vytvořit**. |
| OutputClaim | kontrole | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. Deklarace identity konfigurovaná v `plaintext` inputClaim. |

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **prostý text**:MyPass@word1
  - **sůl**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Deklarace výstupů:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
