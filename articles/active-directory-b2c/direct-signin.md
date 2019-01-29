---
title: Nastavit přímé přihlášení pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak předvyplní přihlašovací jméno nebo přesměrovat přímo do zprostředkovatele sociální identity.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c66fc9ecb608d211c839bab720e8f3f37b96fd5d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169225"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Nastavit přímé přihlášení pomocí Azure Active Directory B2C

Při nastavování přihlášení pro aplikaci pomocí Azure Active Directory (AD) B2C, můžete předem přihlašovacího jména nebo přímé přihlášení k poskytovateli konkrétní sociálních identit, jako je Facebook, LinkedIn nebo účtem Microsoft. 

## <a name="prepopulate-the-sign-in-name"></a>Předvyplní přihlašovací jméno

Během cesty přihlášení uživatele mohou být zaměřeny na aplikaci předávající strany určité uživatele nebo názvu domény. Při cílení na uživatele, můžete určit aplikace, v žádosti o autorizaci `login_hint` dotazování parametr s přihlašovacím jménem uživatele. Azure AD B2C automaticky naplní přihlašovací jméno, když uživatel se musí zadávat heslo.

![pomocí pokynů pro přihlášení](./media/direct-signin/login-hint.png) 

Uživatel je možné změnit hodnotu v textovém poli přihlášení.

Pokud používáte vlastní zásady, má přednost před `SelfAsserted-LocalAccountSignin-Email` technický profil. V `<InputClaims>` nastavte vlastnost DefaultValue signInName deklarace identity do `{OIDC:LoginHint}`. `{OIDC:LoginHint}` Proměnná obsahuje hodnotu `login_hint` parametru. Azure AD B2C přečte hodnotu deklarace identity signInName a předem naplní signInName textového pole.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Přesměrování přihlášení na poskytovatele sociálních sítí

Pokud jste nakonfigurovali přihlašovací cestu pro vaši aplikaci a vložte účtů na sociálních sítích, jako je Facebook, LinkedIn a Google, můžete zadat `domain_hint` parametru. Tento parametr dotazu najdete nápovědu k Azure AD B2C zprostředkovatele sociální identity, který se má použít pro přihlášení. Například, pokud aplikace určuje `domain_hint=facebook.com`, přihlášení přejde přímo na přihlašovací stránku Facebooku.

![pomocí nápovědu domény.](./media/direct-signin/domain-hint.png) 

Pokud používáte vlastní zásady, můžete nakonfigurovat pomocí názvu domény `<Domain>domain name</Domain>` – XML element žádné `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


