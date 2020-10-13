---
title: Podpora AD FS v MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o podpoře Active Directory Federation Services (AD FS) (AD FS) v knihovně Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4107b0785ef4635e0e5e745a35b605d2634a220d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166224"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Podpora Active Directory Federation Services (AD FS) v MSAL.NET
Active Directory Federation Services (AD FS) (AD FS) ve Windows serveru vám umožní přidat ověřování a autorizaci na základě protokolu OAuth 2,0 a povolit aplikace, které vyvíjíte. Tyto aplikace můžou a pak ověřovat uživatele přímo proti AD FS. Další informace najdete v [AD FS scénářích pro vývojáře](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Knihovna Microsoft Authentication Library pro .NET (MSAL.NET) podporuje dva scénáře ověřování proti AD FS:

- MSAL.NET mluví Azure Active Directory, které jsou *federované* s AD FS.
- MSAL.NET mluví **přímo** se autoritou služby ADFS. Tato podpora se podporuje jenom z AD FS 2019 a vyšších. Jedním z scénářů, které tato funkce zvýrazní, je [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) podpora.


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL se připojuje ke službě Azure AD, která je federované s AD FS
MSAL.NET podporuje připojení ke službě Azure AD, která podepisuje spravované uživatele (uživatelé spravováni v Azure AD) nebo federované uživatele (uživatelé spravováni jiným poskytovatelem identity, jako je AD FS). MSAL.NET neví o tom, že jsou uživatelé federované. V takovém případě mluví s Azure AD.

[Autorita](msal-client-application-configuration.md#authority) , kterou použijete v tomto případě, je obvyklá autorita (název hostitele autority + tenant, Common nebo organizace).

### <a name="acquiring-a-token-interactively"></a>Interaktivní získání tokenu
Při volání `AcquireTokenInteractive` metody je činnost koncového uživatele obvykle:

1. Uživatel zadá své ID účtu.
2. Služba Azure AD stručně zobrazí zprávu s odkazem na stránku vaší organizace.
3. Uživatel se přesměruje na přihlašovací stránku zprostředkovatele identity. Přihlašovací stránka se obvykle přizpůsobí s logem organizace.

Podporované verze AD FS v tomto federovaném scénáři jsou AD FS v2, AD FS V3 (Windows Server 2012 R2) a AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Získání tokenu pomocí AcquireTokenByIntegratedAuthentication nebo AcquireTokenByUsernamePassword
Při získávání tokenu pomocí `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` metod nebo MSAL.NET získá poskytovatele identity, aby kontaktoval na základě uživatelského jména.  MSAL.NET po kontaktování zprostředkovatele identity obdrží [token SAML 1,1](reference-saml-tokens.md) .  MSAL.NET pak poskytuje token SAML pro Azure AD jako kontrolní výraz uživatele (podobně jako [u toku](msal-authentication-flows.md#on-behalf-of)spouštěného za běhu), který vrátí token JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL se připojuje přímo k AD FS
MSAL.NET podporuje připojení k AD FS 2019, což je otevřené ID připojení kompatibilní a pochopení PKCE a oborů. Tato podpora vyžaduje, aby se v systému Windows Server používala aktualizace Service Pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) . Při přímém připojení k AD FS autorita, kterou budete chtít použít k sestavení vaší aplikace, je podobná `https://mysite.contoso.com/adfs/` .

V současné době nejsou k dispozici žádné plány pro podporu přímého připojení k:

- AD FS 16, protože nepodporuje PKCE a pořád používá prostředky, ne obor
- AD FS v2, který není kompatibilní s OIDC.

 Pokud potřebujete podporu scénářů vyžadujících přímé připojení k AD FS 2016, použijte nejnovější verzi [knihovny ověřování Azure Active Directory](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Po upgradu místního systému na AD FS 2019 budete moci používat MSAL.NET.

## <a name="next-steps"></a>Další kroky

V případě federovaného případu si přečtěte téma [Konfigurace chování přihlášení Azure Active Directory pro aplikaci pomocí zásad zjišťování domovské sféry](../manage-apps/configure-authentication-for-federated-users-portal.md) .