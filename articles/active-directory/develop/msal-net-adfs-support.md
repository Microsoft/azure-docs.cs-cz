---
title: Podpora služby AD FS v knihovna Microsoft Authentication Library pro .NET | Azure
description: Další informace o podpoře služby Active Directory Federation Services (AD FS) v knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676725"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory Federation Services podporují v MSAL.NET
Active Directory Federation Services (AD FS) v systému Windows Server umožňuje přidávat, OpenID Connect a ověřování na základě OAuth 2.0 a autorizaci pro aplikace, které vyvíjíte a mají tyto aplikace ověřovat uživatele přímo s AD FS. Další informace najdete v článku [scénáře služby AD FS pro vývojáře](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Knihovna Microsoft Authentication Library pro .NET (MSAL.NET) podporuje dva scénáře ověřování služby AD FS:

- Je MSAL.NET přednášky do Azure Active Directory, která sama *federované* se službou AD FS.
- Přednášky MSAL.NET *přímo* pro autoritu služby AD FS, kde je verze služby AD FS OpenID Connect předpisy (spuštění ve službě AD FS 2019). Připojení přímo k AD FS umožňuje MSAL.NET k ověření aplikace spuštěné v [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>Knihovna MSAL připojí k Azure AD, která je Federovaná pomocí služby AD FS
MSAL.NET podporuje připojení k Azure AD, která přihlásí spravované – uživatelé (uživatelé spravovat v Azure AD) nebo federované uživatele (uživatelé spravuje jiného zprostředkovatele identity, jako je například služba AD FS). MSAL.NET neví o tom, že jsou federované uživatele. Jde to je, a do služby Azure AD.

[Autority](msal-client-application-configuration.md#authority) můžete použít v tomto případě je obvykle autoritu (název hostitele autority + tenanta, běžné nebo organizace).

### <a name="acquiring-a-token-interactively"></a>Získávání tokenu interaktivně
Při volání `AcquireTokenInteractive` metoda, činnost koncového uživatele je obvykle:

1. Uživatel zadá své ID účtu.
2. Azure AD krátce zobrazí zpráva "Probíhá přechod na stránku vaší organizace".
3. Uživatel je přesměrován na přihlašovací stránce zprostředkovatele identity. Přihlašovací stránka je obvykle přizpůsobit pomocí loga organizace.

Podporované verze AD FS v tomto scénáři federované jsou v2 služby AD FS, AD FS v3 (Windows Server 2012 R2) a služby AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Získání tokenu pomocí AcquireTokenByIntegratedAuthentication nebo AcquireTokenByUsernamePassword
Při získávání tokenu pomocí `AcquireTokenByIntegratedAuthentication` nebo `AcquireTokenByUsernamePassword` metody, MSAL.NET získá zprostředkovatele identity, který se obraťte se na uživatelské jméno.  Přijímá MSAL.NET [token SAML 1.1](reference-saml-tokens.md) po kontaktovat zprostředkovatele identity.  MSAL.NET poskytuje tokenu SAML do služby Azure AD jako kontrolní výraz uživatele (podobně jako [tok on-behalf-of](msal-authentication-flows.md#on-behalf-of)) Chcete-li získat zpět token JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>Knihovna MSAL připojuje přímo ke službě AD FS
MSAL.NET podporuje připojení k AD FS 2019, což je Open ID Connect kompatibilní. Při přímém připojení k AD FS, autority budete chtít použít k sestavení aplikace je podobný `https://mysite.contoso.com/adfs/`.

V současné době nejsou žádné plány pro podporu přímé připojení k AD FS 2016 nebo služby AD FS v2 (které nejsou OpenID Connect kompatibilní). Pokud potřebujete podporovat scénářům, které vyžadují přímé připojení ke službě AD FS 2016, použijte prosím nejnovější verzi [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Při upgradu vašeho místního systému do služby AD FS 2019, budete moct pomocí MSAL.NET.
