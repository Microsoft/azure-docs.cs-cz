---
title: Nastavení přímého přihlášení pomocí Azure Active Directory B2C | Microsoft Docs
description: Naučte se předem vyplnit přihlašovací jméno nebo přesměrovat přímo na zprostředkovatele sociální identity.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388591"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Nastavení přímého přihlášení pomocí Azure Active Directory B2C

Při nastavování přihlášení aplikace pomocí Azure Active Directory (AD) B2C můžete předem vyplnit přihlašovací jméno nebo přímé přihlášení ke konkrétnímu poskytovateli sociálních identit, jako je Facebook, LinkedIn nebo účet Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Předem naplnit přihlašovací jméno

Během cesty uživatele přihlašování se může aplikace předávající strany zaměřit na konkrétního uživatele nebo název domény. Při cílení na uživatele může aplikace v žádosti o autorizaci zadat `login_hint` parametr dotazu s přihlašovacím jménem uživatele. Azure AD B2C automaticky vyplní přihlašovací jméno, zatímco uživatel potřebuje zadat heslo.

![Přihlašovací stránka pro registraci se zvýrazněným parametrem login_hint dotazu v adrese URL](./media/direct-signin/login-hint.png)

Uživatel může změnit hodnotu v textovém poli přihlášení.

Pokud používáte vlastní zásadu, přepište `SelfAsserted-LocalAccountSignin-Email` technický profil. V `<InputClaims>` části nastavte vlastnost DefaultValue deklarace identity signInName na `{OIDC:LoginHint}` . `{OIDC:LoginHint}`Proměnná obsahuje hodnotu `login_hint` parametru. Azure AD B2C přečte hodnotu deklarace identity signInName a předem naplní textové pole signInName.

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

Pokud jste nakonfigurovali cestu pro přihlášení k vaší aplikaci, aby zahrnovala účty sociálních sítí, jako je Facebook, LinkedIn nebo Google, můžete zadat `domain_hint` parametr. Tento parametr dotazu poskytuje nápovědu pro Azure AD B2C o poskytovateli sociální identity, který by se měl použít pro přihlášení. Například pokud aplikace určuje `domain_hint=facebook.com` , přihlašování se přímo na přihlašovací stránku Facebooku.

![Přihlašovací stránka pro registraci se zvýrazněným parametrem domain_hint dotazu v adrese URL](./media/direct-signin/domain-hint.png)

Pokud používáte vlastní zásadu, můžete nakonfigurovat název domény pomocí `<Domain>domain name</Domain>` XML elementu Any `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


