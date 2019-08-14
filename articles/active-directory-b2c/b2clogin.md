---
title: Nastavte adresy URL pro přesměrování na b2clogin.com-Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si o používání b2clogin.com v adresách URL pro přesměrování pro Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927289"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Nastavte adresy URL pro přesměrování na b2clogin.com pro Azure Active Directory B2C

Když nastavíte poskytovatele identity pro registraci a přihlášení do aplikace Azure Active Directory (Azure AD) B2C, musíte zadat adresu URL pro přesměrování. V minulosti jsme použili login.microsoftonline.com, teď byste měli používat b2clogin.com.

> [!NOTE]
> V b2clogin.com můžete použít JavaScriptový kód na straně klienta (v současnosti ve verzi Preview). Pokud používáte login.microsoftonline.com, váš kód JavaScriptu se odebere z vlastní stránky. Další omezení zabezpečení se také vztahují na login.microsoftonline.com, jako je například odebírání prvků formuláře HTML z vlastní stránky. 

Použití b2clogin.com přináší další výhody, jako například:

- Místo spotřebované v hlavičce souboru cookie od služby společnosti Microsoft se sníží.
- Vaše adresy URL už neobsahují odkaz na Microsoft. Například, `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

> [!NOTE]
> Název tenanta i identifikátor GUID tenanta můžete použít následujícím způsobem:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(která pořád odkazuje na `onmicrosoft.com`)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(v takovém případě neexistuje žádný odkaz na společnost Microsoft.)
>
> Pro vašeho tenanta Azure Active Directory B2C ale nemůžete použít _vlastní doménu_ , třeba `https://your-tenant-name.b2clogin.com/your-custom-domain-name` by nefungovala.

Vezměte v úvahu tato nastavení, která se při použití b2clogin.com můžou potřebovat změnit:

- Nastavte adresy URL pro přesměrování v aplikacích zprostředkovatele identity tak, aby používaly b2clogin.com. 
- Nastavte aplikaci Azure AD B2C tak, aby používala b2clogin.com pro odkazy na uživatelský tok a koncové body tokenu. 
- Pokud používáte MSAL, musíte nastavit vlastnost **ValidateAuthority** na `false`.
- Ujistěte se, že jste změnili všechny **Povolené zdroje** , které jste definovali v nastavení CORS pro [přizpůsobení uživatelského rozhraní](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Změnit adresy URL pro přesměrování

Chcete-li použít b2clogin.com, vyhledejte v nastavení aplikace zprostředkovatele identity a změňte seznam důvěryhodných adres URL pro přesměrování zpět na Azure AD B2C.  V současné době máte pravděpodobně nastavené přesměrování zpět na některé login.microsoftonline.com lokality. 

Bude nutné změnit adresu URL pro přesměrování, `your-tenant-name.b2clogin.com` aby byla ověřena. Ujistěte se, že `your-tenant-name` jste nahradili názvem vašeho tenanta Azure AD B2C a odeberete `/te` , pokud existuje v adrese URL. Existují mírné variace této adresy URL pro každého zprostředkovatele identity, takže na příslušné stránce můžete získat přesnou adresu URL.

Informace o nastavení pro zprostředkovatele identity můžete najít v následujících článcích:

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

Vaše aplikace Azure AD B2C pravděpodobně odkazuje na `login.microsoftonline.com` několik míst, jako jsou například odkazy na tok uživatele a koncové body tokenu.  Ujistěte se, že jste aktualizovali koncový bod autorizace, koncový bod tokenu a vystavitele pro použití `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Nastavení vlastnosti ValidateAuthority

Pokud používáte MSAL, nastavte vlastnost **ValidateAuthority** na `false`. Pokud je **ValidateAuthority** nastaveno na `false`, přesměrování mohou b2clogin.com. 

Následující příklad ukazuje, jak můžete nastavit vlastnost:

V [MSAL pro .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

A v [MSAL pro JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
