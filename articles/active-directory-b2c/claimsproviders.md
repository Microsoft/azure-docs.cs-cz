---
title: ClaimsProviders-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element ClaimsProvider vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 846394266b981c14788148be465912b14bc1fb3e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447907"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Deklarace identity poskytují rozhraní pro komunikaci s různými typy stran prostřednictvím svých [technických profilů](technicalprofiles.md). Každý zprostředkovatel deklarací identity musí mít jeden nebo více technických profilů, které určují koncové body a protokoly potřebné ke komunikaci se zprostředkovatelem deklarací. Zprostředkovatel deklarací může mít více technických profilů. Například může být definováno více technických profilů, protože zprostředkovatel deklarací podporuje více protokolů, různé koncové body s různými možnostmi nebo uvolňuje různé nároky na různé úrovně záruky. Může být přijatelné vydávat citlivé deklarace identity na jednu cestu uživatele, ale ne v jiném.

Cesta uživatele kombinuje volání technických profilů prostřednictvím kroků orchestrace k definování obchodní logiky. 

```xml
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

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Akreditovaný zprostředkovatel deklarací identity, který se dá využít v různých jízdách uživatelů |

## <a name="claimsprovider"></a>ClaimsProvider

Element **ClaimsProvider** obsahuje následující podřízené prvky:

| Prvek | Výskytů | Popis |
| ------- | ---------- | ----------- |
| Doména | 0:1 | Řetězec, který obsahuje název domény pro poskytovatele deklarací identity. Například pokud váš zprostředkovatel deklarací zahrnuje technický profil Facebooku, název domény je Facebook.com. Tento název domény se používá pro všechny technické profily definované ve zprostředkovateli deklarací, pokud není přepsán technickým profilem. Na název domény se taky dá odkazovat v **domain_hint**. Další informace najdete v části **přihlášení k přesměrování do poskytovatele sociálních sítí** v tématu [Nastavení přímého přihlašování pomocí Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Řetězec, který obsahuje název zprostředkovatele deklarací. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Sada technických profilů podporovaná zprostředkovatelem deklarací identity |

**ClaimsProvider** organizuje, jak se vaše technické profily vztahují k poskytovateli deklarací identity. Následující příklad ukazuje zprostředkovatele deklarací Azure Active Directory s Azure Active Directory technickými profily:

```xml
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

```xml
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
