---
title: ClaimsProviders-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element ClaimsProvider vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bda94344a22f39daf104e7b95e33723187c4aa1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983006"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zprostředkovatel deklarací obsahuje sadu [technických profilů](technicalprofiles.md). Každý zprostředkovatel deklarací identity musí mít jeden nebo více technických profilů, které určují koncové body a protokoly potřebné ke komunikaci se zprostředkovatelem deklarací. Zprostředkovatel deklarací může mít více technických profilů. Například může být definováno více technických profilů, protože zprostředkovatel deklarací podporuje více protokolů, různé koncové body s různými možnostmi nebo uvolňuje různé nároky na různé úrovně záruky. Může být přijatelné vydávat citlivé deklarace identity na jednu cestu uživatele, ale ne v jiném.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

Element **ClaimsProviders** obsahuje následující element:

| Element | Výskytů | Popis |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Akreditovaný zprostředkovatel deklarací identity, který se dá využít v různých jízdách uživatelů |

## <a name="claimsprovider"></a>ClaimsProvider

Element **ClaimsProvider** obsahuje následující podřízené prvky:

| Element | Výskytů | Popis |
| ------- | ---------- | ----------- |
| Domain (Doména) | 0:1 | Řetězec, který obsahuje název domény pro poskytovatele deklarací identity. Například pokud váš zprostředkovatel deklarací zahrnuje technický profil Facebooku, název domény je Facebook.com. Tento název domény se používá pro všechny technické profily definované ve zprostředkovateli deklarací, pokud není přepsán technickým profilem. Na název domény se taky dá odkazovat v **domain_hint**. Další informace najdete v části **přihlášení k přesměrování do poskytovatele sociálních sítí** v tématu [Nastavení přímého přihlašování pomocí Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Řetězec, který obsahuje název zprostředkovatele deklarací. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Sada technických profilů podporovaná zprostředkovatelem deklarací identity |

**ClaimsProvider** organizuje, jak se vaše technické profily vztahují k poskytovateli deklarací identity. Následující příklad ukazuje zprostředkovatele deklarací Azure Active Directory s Azure Active Directory technickými profily:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Následující příklad ukazuje zprostředkovatele deklarací Facebooku s technickým profilem **Facebooku-OAuth** .

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
