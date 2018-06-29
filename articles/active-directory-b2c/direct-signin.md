---
title: Nastavit přímé přihlášení pomocí Azure Active Directory B2C | Microsoft Docs
description: Zjistěte, jak předvyplní přihlašovací jméno nebo přesměrovat přímo k poskytovateli sociální identity.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/18/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc3baa8fe4139acec94a722bf8c2bccb708a9470
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102173"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Nastavit přímé přihlášení pomocí Azure Active Directory B2C

Při nastavování přihlašování pro vaše aplikace pomocí Azure Active Directory (AD) B2C, můžete předem zadat přihlašovací jméno nebo přímé přihlášení k poskytovateli konkrétní sociálních identity, jako je Facebook, LinkedIn nebo účtu Microsoft. 

## <a name="prepopulate-the-sign-in-name"></a>Předvyplní přihlašovací jméno

Během přihlášení uživatele cesty aplikace předávající strany mohou být zaměřeny určité uživatele nebo názvu domény. Pokud je cílem uživatele, můžete určit aplikace, v žádosti o autorizaci `login_hint` parametr s názvem přihlášení uživatele dotazu. Azure AD B2C automaticky naplní název přihlašování, když uživatel se musí k zadání hesla.

![Použití pomocného parametru přihlášení](./media/direct-signin/login-hint.png) 

Je možné změnit hodnotu v textovém poli přihlášení uživatele.

Pokud používáte vlastní zásadu, mají přednost před `SelfAsserted-LocalAccountSignin-Email` technické profilu. V `<InputClaims>` část, nastavte hodnotu DefaultValue deklarace signInName na `{OIDC:LoginHint}`. `{OIDC:LoginHint}` Proměnná obsahuje hodnotu `login_hint` parametr. Azure AD B2C čte hodnoty deklarace identity signInName a předem naplní signInName textové pole.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Přesměrování přihlášení k poskytovateli sociální

Pokud jste nakonfigurovali cesty přihlášení pro vaši aplikaci, aby zahrnovala sociálních účty, jako je Facebook, LinkedIn nebo Google, můžete zadat `domain_hint` parametr. Tento parametr dotazu poskytuje nápovědu k Azure AD B2C o poskytovateli sociální identity, který se má použít pro přihlášení. Například pokud aplikace určí `domain_hint=facebook.com`, přihlášení přejde přímo na přihlašovací stránku služby Facebook.

![Použití pomocného parametru domény](./media/direct-signin/domain-hint.png) 

Pokud používáte vlastní zásady, můžete nakonfigurovat pomocí názvu domény `<Domain>domain name</Domain>` – element XML libovolného `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


