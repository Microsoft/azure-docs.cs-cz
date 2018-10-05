---
title: Sada přesměrovaly na b2clogin.com pro Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o použití b2clogin.com do vaší přesměrovat adresy URL pro Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e06cf1a443d4fd158e29ef4b53206a83800dfe9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803048"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Sada přesměrovaly na b2clogin.com pro Azure Active Directory B2C

Když nastavíte zprostředkovatele identity pro registraci a přihlašování v aplikaci Azure Active Directory (Azure AD) B2C, budete muset zadat adresu URL přesměrování. V minulosti bylo použito login.microsoftonline.com, teď by měl být použití b2clogin.com.

Použití b2clogin.com poskytuje další výhody, jako například:

- Soubory cookie jsou již sdíleny s jinými službami Microsoftu.
- Vaší adresy URL už nebude obsahovat odkaz na Microsoft. Například, `https://your-tenant-name.b2clogin.com/tfp/your-tenant-ID/policyname/v2.0/.well-known/openid-configuration`.

Chcete-li použít b2clogin.com, nastavte přesměrování adresy URL ve vašich aplikacích zprostředkovatele identity použití b2clogin.com. Můžete také nastavit aplikace Azure AD B2C použití b2clogin.com pro token koncových bodů a odkazy na zásady. Pokud používáte MSAL, je nutné nastavit **ValidateAuthority** vlastnost `false`.

## <a name="change-redirect-urls"></a>Změna adresy URL pro přesměrování

Použití b2clogin.com, v nastavení pro vaši aplikaci zprostředkovatele identity, vyhledejte a změnit seznam důvěryhodných adres URL pro přesměrování zpět do Azure AD B2C.  V současné době máte pravděpodobně ji nastavit na přesměrovat zpět na některé login.microsoftonline.com lokality. 

Budete muset změnit adresu URL přesměrování tak, aby `your-tenant-name.b2clogin.com` autorizaci. Nezapomeňte nahradit `your-tenant-name` s názvem vaší Azure AD B2C tenanta a odeberte `/te` pokud existuje v adrese URL. Existují malé odchylky na tuto adresu URL pro každého zprostředkovatele identity proto zkontrolujte na odpovídající stránce zobrazíte přesnou adresu URL.

Informace o nastavení pro zprostředkovatele identity najdete v následujících článcích:

- [Účet Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Vlastní OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Aktualizace aplikace

Aplikace Azure AD B2C pravděpodobně odkazuje na `login.microsoftonline.com` na několika místech, jako jsou odkazy na zásady a koncové body tokenu.  Ujistěte se, že koncový bod autorizace, koncový bod tokenu a vystavitele byla aktualizována, aby používat `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Nastavte vlastnost ValidateAuthority

Pokud používáte MSAL, nastavte **ValidateAuthority** k `false`. Následující příklad ukazuje, jak může nastavit vlastnost:

```
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

 Další informace najdete v tématu [ClientApplicationBase třídy ](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).