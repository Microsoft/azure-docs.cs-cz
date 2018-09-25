---
title: Použití b2clogin.com | Dokumentace Microsoftu
description: Další informace o použití b2clogin.com místo login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c24582fce44006d9a3972d73078aa8cb0d212c11
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053840"
---
# <a name="using-b2clogincom"></a>Použití B2Clogin.com

Od této chvíle, už jsme podporu všem zákazníkům používat `<YourDirectoryName>.b2clogin.com` a vyřadíme `login.microsoftonline.com`. B2Clogin.com poskytuje další výhody, jako:
* Stejné soubory cookie se už nebude sdílet s jinými službami Microsoftu.
* V adrese URL můžete odebrat všechny odkazy na Microsoft (můžete nahradit `<YourDirectoryName>.onmicrosoft.com` své ID adresáře). Například: `https://<YourDirectoryName>.b2clogin.com/tfp/<YourDirectoryID>/<policyname>/v2.0/.well-known/openid-configuration`.

Zde je, co je potřeba k migraci na b2clogin.com

* Změnit identifikátory URI pro přesměrování pro vaše aplikace zprostředkovatele identity v sociálních sítích
* Upravit aplikace použije B2Clogin.com místo `login.microsoftonline.com` pro token koncových bodů a odkazy na zásady.
* Pokud používáte MSAL, je nutné nastavit `ValidateAuthority=false`.  

##<a name="redirect-uris-for-social-identity-providers"></a>Identifikátory URI pro přesměrování pro zprostředkovatele identity v sociálních sítích

Pokud máte nastavení ve vašem adresáři zprostředkovatelé identity účtu na sociální síti bude nutné provést změny ve svých aplikacích.  Neexistuje parametr pro aplikaci pro každý sociálních sítí poskytovatele, který obsahuje seznam důvěryhodných adres URL pro přesměrování Azure AD B2C.  V současné době máte pravděpodobně ji nastavit na některé přesměrovat `login.microsoftonline.com` lokality, bude nutné změnit tuto adresu URL tak, aby `YourDirectoryName.b2clogin.com` budou autorizovaní identifikátor URI přesměrování.  Ujistěte se, že chcete odebrat `/te` také.  Existují malé odchylky na tuto adresu URL pro každého zprostředkovatele identity proto zkontrolujte na odpovídající stránce zobrazíte přesnou adresu URL.  

| Zprostředkovatel identity |
|-------------------|
|[Účet Microsoft](active-directory-b2c-setup-msa-app.md)|
|[Facebook](active-directory-b2c-setup-fb-app.md)|
|[Google](active-directory-b2c-setup-goog-app.md)|
|[Amazon](active-directory-b2c-setup-amzn-app.md)|
|[LinkedIn](active-directory-b2c-setup-li-app.md)|
|[Twitter](active-directory-b2c-setup-twitter-app.md)|
|[GitHub](active-directory-b2c-setup-github-app.md)|
|[Weibo](active-directory-b2c-setup-weibo-app.md)|
|[QQ](active-directory-b2c-setup-qq-app.md)|
|[WeChat](active-directory-b2c-setup-wechat-app.md)|
|[Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)|
|[Vlastní OIDC](active-directory-b2c-setup-oidc-idp.md)|

##<a name="update-your-application-references"></a>Aktualizovat odkazy na vaše aplikace

Vaše aplikaci pravděpodobně odkazuje na `login.microsoftonline.com` na několika místech, jako jsou odkazy na zásady a koncové body tokenu.  Ujistěte se, že koncový bod autorizace, koncový bod tokenu a vystavitele byly aktualizovány.  

##<a name="set-validateauthorityfalse-in-msal"></a>Nastavte `ValidateAuthority=false` v MSAL

Pokud používáte MSAL, budete muset nastavit `ValidateAuthority=false`.  Další informace najdete v tématu [této dokumentace](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).