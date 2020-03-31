---
title: Zprostředkovatelé deklarací identity – služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Zadejte claimsprovider prvek vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189765"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zprostředkovatel deklarací identity obsahuje sadu [technických profilů](technicalprofiles.md). Každý zprostředkovatel deklarací identity musí mít jeden nebo více technických profilů, které určují koncové body a protokoly potřebné ke komunikaci s poskytovatelem deklarací identity. Zprostředkovatel deklarací identity může mít více technických profilů. Například může být definováno více technických profilů, protože zprostředkovatel deklarací identity podporuje více protokolů, různé koncové body s různými možnostmi nebo vydává různé deklarace identity na různých úrovních záruky. Může být přijatelné uvolnit citlivé deklarace identity v jedné cestě uživatele, ale ne v jiném.

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

**ClaimsProviders** Prvek obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Zprostředkovatel pohledávek | 1:n | Akreditovaný poskytovatel deklarací identity, který lze využít v různých cestách uživatelů. |

## <a name="claimsprovider"></a>Zprostředkovatel pohledávek

Identity **Identity Prvek** obsahuje následující podřízené prvky:

| Element | Výskyty | Popis |
| ------- | ---------- | ----------- |
| Domain (Doména) | 0:1 | Řetězec, který obsahuje název domény pro zprostředkovatele deklarací. Pokud například váš poskytovatel deklarací identity zahrnuje technický profil facebooku, název domény se Facebook.com. Tento název domény se používá pro všechny technické profily definované ve zprostředkovateli deklarací identity, pokud není přepsán technickým profilem. Název domény lze také odkazovat v **domain_hint**. Další informace najdete v části **Přesměrování přihlášení k poskytovateli sociálních služeb** v části Nastavení [přímého přihlášení pomocí služby Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Řetězec, který obsahuje název zprostředkovatele deklarací identity. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Sada technických profilů podporovaných poskytovatelem deklarací |

**ClaimsProvider** uspořádá, jak se vaše technické profily vztahují k poskytovateli deklarací identity. Následující příklad ukazuje poskytovatele deklarací identity služby Azure Active Directory s technickými profily služby Azure Active Directory:

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

Následující příklad ukazuje poskytovatele nároků na Facebooku s technickým profilem **Facebook-OAUTH.**

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
