---
title: Nastavení přímého přihlášení pomocí Azure Active Directory B2C | Microsoft Docs
description: Naučte se předem vyplnit přihlašovací jméno nebo přesměrovat přímo na zprostředkovatele sociální identity.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8c0f3d8f3f49001e1326688ccc794e19d1148e5d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846893"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Nastavení přímého přihlášení pomocí Azure Active Directory B2C

Při nastavování přihlášení aplikace pomocí Azure Active Directory (AD) B2C můžete předem vyplnit přihlašovací jméno nebo přímé přihlášení ke konkrétnímu poskytovateli sociálních identit, jako je Facebook, LinkedIn nebo účet Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Předem naplnit přihlašovací jméno

Během cesty uživatele přihlašování se může aplikace předávající strany zaměřit na konkrétního uživatele nebo název domény. Při cílení na uživatele může aplikace v žádosti o `login_hint` autorizaci zadat parametr dotazu s přihlašovacím jménem uživatele. Azure AD B2C automaticky vyplní přihlašovací jméno, zatímco uživatel potřebuje zadat heslo.

![Přihlašovací stránka pro registraci s parametrem login_hint dotazu, který se zvýrazní v adrese URL](./media/direct-signin/login-hint.png)

Uživatel může změnit hodnotu v textovém poli přihlášení.

Pokud používáte vlastní zásadu, přepište `SelfAsserted-LocalAccountSignin-Email` technický profil. V části nastavte vlastnost DefaultValue deklarace identity signInName na `{OIDC:LoginHint}`. `<InputClaims>` Proměnná obsahuje hodnotu `login_hint`parametru. `{OIDC:LoginHint}` Azure AD B2C přečte hodnotu deklarace identity signInName a předem naplní textové pole signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Přesměrování přihlášení k poskytovateli sociálních sítí

Pokud jste nakonfigurovali cestu pro přihlášení k vaší aplikaci, aby zahrnovala účty sociálních sítí, jako je Facebook, LinkedIn nebo Google, můžete zadat `domain_hint` parametr. Tento parametr dotazu poskytuje nápovědu pro Azure AD B2C o poskytovateli sociální identity, který by se měl použít pro přihlášení. Například pokud aplikace určuje `domain_hint=facebook.com`, přihlašování se přímo na přihlašovací stránku Facebooku.

![Přihlašovací stránka pro registraci s parametrem domain_hint dotazu, který se zvýrazní v adrese URL](./media/direct-signin/domain-hint.png)

Pokud používáte vlastní zásadu, můžete nakonfigurovat název domény pomocí `<Domain>domain name</Domain>` XML elementu any. `<ClaimsProvider>`

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


