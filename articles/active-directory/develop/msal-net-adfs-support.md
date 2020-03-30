---
title: Podpora AD FS v MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Informace o podpoře služby AD FS (Active Directory Federation Services) v knihovně Ověřování microsoftu pro rozhraní .NET (MSAL.NET).
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
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160755"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Podpora služby AD FS v MSAL.NET
Služba AD FS (AD FS) služby Active Directory umožňuje přidat ověřování a autorizaci na základě protokolu OpenID Connect a OAuth 2.0 do aplikací, které vyvíjíte. Tyto aplikace pak mohou ověřovat uživatele přímo proti ad FS. Další informace naleznete v části [Scénáře ad FS pro vývojáře](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Microsoft Authentication Library pro .NET (MSAL.NET) podporuje dva scénáře ověřování ve službě AD FS:

- MSAL.NET bude mluvit se službou Azure Active Directory, která je sama *federována* službou AD FS.
- MSAL.NET **přímo** k orgánu ADFS. To je podporováno pouze od AD FS 2019 a výše. Jedním ze scénářů, které zdůrazňuje, je podpora [Azure Stacku](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL se připojuje k Azure AD, který je federován pomocí služby AD FS
MSAL.NET podporuje připojení k Azure AD, který se přihlásí ve spravovaných uživatelích (uživatelé spravovaní ve službě Azure AD) nebo federovaných uživatelů (uživatelé spravovaní jiným poskytovatelem identity, jako je služba AD FS). MSAL.NET neví o tom, že uživatelé jsou federovány. Pokud jde o to, že mluví s Azure AD.

[Autorita,](msal-client-application-configuration.md#authority) kterou v tomto případě používáte, je obvyklá autorita (název hostitele autority + tenant, běžný nebo organizace).

### <a name="acquiring-a-token-interactively"></a>Interaktivní získání tokenu
Při volání `AcquireTokenInteractive` metody je uživatelské prostředí obvykle:

1. Uživatel zadá ID svého účtu.
2. Azure AD krátce zobrazí zprávu "Převzít na stránku vaší organizace".
3. Uživatel je přesměrován na přihlašovací stránku zprostředkovatele identity. Přihlašovací stránka je obvykle přizpůsobena logem organizace.

Podporované verze služby AD FS v tomto federovaném scénáři jsou AD FS v2, AD FS v3 (Windows Server 2012 R2) a AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Získání tokenu pomocí AcquireTokenByIntegratedAuthentication nebo AcquireTokenByUsernamePassword
Při získávání tokenu `AcquireTokenByIntegratedAuthentication` pomocí `AcquireTokenByUsernamePassword` metody nebo MSAL.NET získá zprostředkovatele identity kontaktovat na základě uživatelského jména.  MSAL.NET obdrží [token SAML 1.1](reference-saml-tokens.md) po kontaktování zprostředkovatele identity.  MSAL.NET pak poskytuje token SAML do Služby Azure AD jako kontrolní výraz uživatele (podobně jako [tok](msal-authentication-flows.md#on-behalf-of)) získat zpět JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL se připojuje přímo ke službě AD FS
MSAL.NET podporuje připojení k AD FS 2019, což je kompatibilní s Open ID Connect a rozumí PKCE a oborům. Tato podpora vyžaduje, aby byla v systému Windows Server použita aktualizace Service Pack [KB 4490481.](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) Při přímém připojení ke službě AD FS je autorita, kterou budete `https://mysite.contoso.com/adfs/`chtít použít k vytvoření aplikace, podobná .

V současné době neexistují žádné plány na podporu přímého připojení k:

- AD FS 16, protože nepodporuje PKCE a stále používá prostředky, nikoli obor
- AD FS v2, který není kompatibilní s OIDC.

 Pokud potřebujete podporovat scénáře vyžadující přímé připojení ke službě AD FS 2016, použijte nejnovější verzi [Azure Active Directory Authentication Library](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Po upgradu místního systému na AD FS 2019 budete moct používat MSAL.NET.

## <a name="next-steps"></a>Další kroky

Federovaný případ najdete v [tématu Konfigurace chování přihlášení služby Azure Active Directory pro aplikaci pomocí zásad zjišťování domovské sféry.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
