---
title: Nastavení přímého přihlášení pomocí služby Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si, jak předem vyplnit přihlašovací jméno nebo přesměrovat přímo na poskytovatele sociální identity.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a02ad3ea43ae9d91489417bc314e3c23d54a958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188762"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Nastavení přímého přihlášení pomocí služby Azure Active Directory B2C

Při nastavování přihlášení pro vaši aplikaci pomocí Služby Azure Active Directory (AD) B2C můžete předem vyplnit přihlašovací jméno nebo přímé přihlášení ke konkrétnímu poskytovateli sociální identity, jako je Facebook, LinkedIn nebo účet Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Předem naplnit přihlašovací jméno

Během cesty přihlašovacího uživatele může aplikace předávající strany cílit na konkrétní název uživatele nebo domény. Při cílení na uživatele může aplikace v žádosti `login_hint` o autorizaci zadat parametr dotazu s přihlašovacím jménem uživatele. Azure AD B2C automaticky vyplní přihlašovací jméno, zatímco uživatel potřebuje pouze zadat heslo.

![Zaregistrovat přihlašovací stránku se zvýrazněným login_hint dotazem v adrese URL](./media/direct-signin/login-hint.png)

Uživatel je schopen změnit hodnotu v přihlašovacím textovém poli.

Pokud používáte vlastní zásady, `SelfAsserted-LocalAccountSignin-Email` přepište technický profil. V `<InputClaims>` části nastavte výchozí hodnotu deklarace signInName na `{OIDC:LoginHint}`. Proměnná `{OIDC:LoginHint}` obsahuje hodnotu `login_hint` parametru. Azure AD B2C přečte hodnotu deklarace signInName a předem vyplní textové pole signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Přesměrování přihlášení na poskytovatele sociální chod

Pokud jste pro vaši aplikaci nakonfigurovali cestu přihlášení tak, aby zahrnovala sociální `domain_hint` účty, jako je Facebook, LinkedIn nebo Google, můžete parametr zadat. Tento parametr dotazu poskytuje nápovědu k Azure AD B2C o zprostředkovatele sociální identity, který by měl být použit pro přihlášení. Pokud například aplikace určuje `domain_hint=facebook.com`, přihlášení přejde přímo na přihlašovací stránku Facebooku.

![Zaregistrovat přihlašovací stránku se zvýrazněným domain_hint dotazem v adrese URL](./media/direct-signin/domain-hint.png)

Pokud používáte vlastní zásady, můžete název domény `<Domain>domain name</Domain>` nakonfigurovat `<ClaimsProvider>`pomocí elementu XML libovolného souboru .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


