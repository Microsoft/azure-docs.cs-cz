---
title: Obecné příklady transformace deklarací pro vlastní zásady
titleSuffix: Azure AD B2C
description: Obecné příklady transformace deklarací identity framework (IEF) schéma Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188541"
---
# <a name="general-claims-transformations"></a>Obecné transformace nároků

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady pro použití obecných deklarací identity transformace schématu identity experience framework v Azure Active Directory B2C (Azure AD B2C). Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>Kopírovat nárok

Zkopírujte hodnotu deklarace pohledávky do jiného. Oba deklarace musí být ze stejného typu.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | řetězec, int | Typ deklarace, který má být zkopírován. |
| Výstupní nárok | outputClaim | řetězec, int | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |

Pomocí této transformace deklarací zkopírujte hodnotu z řetězce nebo číselné deklarace do jiné deklarace identity. Následující příklad zkopíruje hodnotu deklarace e-mailu externalEmail.

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

### <a name="example"></a>Příklad

- Vstupní deklarace:
    - **inputClaim**:bob@contoso.com
- Výstupní nároky:
    - **outputClaim**:bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

Zkontroluje, zda **inputClaim** existuje nebo ne a nastaví **outputClaim** na true nebo false odpovídajícím způsobem.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim |Všechny | Vstupní tvrzení, jehož existence musí být ověřena. |
| Výstupní nárok | outputClaim | Boolean | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |

Pomocí této transformace deklarací zkontrolujte, zda deklarace existuje nebo obsahuje libovolnou hodnotu. Vrácená hodnota je logická hodnota, která označuje, zda deklarace existuje. Následující příklad zkontroluje, zda e-mailová adresa existuje.

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

- Vstupní deklarace:
  - **inputClaim**:someone@contoso.com
- Výstupní nároky:
  - **outputClaim**: true outputClaim : true outputClaim : true outputClaim

## <a name="hash"></a>Hodnota hash

Hash za předpokladu, prostý text pomocí soli a tajemství. Použitý algoritmus hash je SHA-256.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | Nešifrovaném | řetězec | Vstupní deklarace, která má být zašifrována |
| Vstupní deklarace | Sůl | řetězec | Parametr soli. Můžete vytvořit náhodnou hodnotu pomocí `CreateRandomString` transformace deklarací. |
| Parametr Input | randomizerSecret | řetězec | Odkazuje na existující **klíč zásad**Azure AD B2C . Chcete-li vytvořit nový klíč zásad: V tenantovi Azure AD B2C vyberte v části **Správa** **vyberte rozhraní Identity Experience Framework**. Vyberte **klávesy zásad,** chcete-li zobrazit klíče, které jsou k dispozici ve vašem tenantovi. Vyberte **Přidat**. V **části Možnosti**vyberte **možnost Ručně**. Zadejte název (předpona *B2C_1A_* může být přidána automaticky.). Do textového pole **Tajné** zadejte libovolný tajný klíč, který chcete použít, například 1234567890. V **části Použití klíče**vyberte možnost **Podpis**. Vyberte **Vytvořit**. |
| Výstupní nárok | hash | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána. Deklarace nakonfigurovaná v `plaintext` inputClaim. |

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

- Vstupní deklarace:
  - **prostý text**:MyPass@word1
  - **sůl**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Výstupní nároky:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZKjuxGMWhA5YQNihzV6U=
