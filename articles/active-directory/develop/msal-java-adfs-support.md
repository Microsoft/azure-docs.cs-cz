---
title: Podpora služby AD FS (MSAL pro Javu)
titleSuffix: Microsoft identity platform
description: Informace o podpoře služby AD FS (Active Directory Federation Services) v knihovně Microsoft Authentication Library for Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696210"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Podpora služby Active Directory Federation Services v jazyce MSAL pro jazyk Java

Služba AD FS (AD FS) služby Active Directory (AD FS) v systému Windows Server umožňuje přidat ověřování a autorizaci na základě technologie OpenID Connect a OAuth 2.0 do aplikace Microsoft Authentication Library for Java (MSAL for Java). Po integraci může vaše aplikace ověřovat uživatele ve službě AD FS, která je federovaná prostřednictvím Azure AD. Další informace o scénářích naleznete [v tématu AD FS Scenarios for Developers](/windows-server/identity/ad-fs/ad-fs-development).

Aplikace, která používá MSAL pro Java bude mluvit s Azure Active Directory (Azure AD), který pak federates do AD FS.

MSAL pro Java se připojuje k Azure AD, který se přihlásí k uživatelům, kteří jsou spravovaní ve službě Azure AD (spravovaní uživatelé) nebo uživatelé spravovaní jiným poskytovatelem identity, jako je služba AD FS (federovaní uživatelé). MSAL pro Java neví, že uživatel je federovaný. Jednoduše promluví se službou Azure AD.

[Autorita,](msal-client-application-configuration.md#authority) kterou v tomto případě používáte, je obvyklá autorita (název hostitele autority + tenant, běžný nebo organizace).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Získání tokenu interaktivně pro federovaného uživatele

Při volání `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` nebo `AuthorizationCodeParameters` `DeviceCodeParameters`s nebo , uživatelské prostředí je obvykle:

1. Uživatel zadá ID svého účtu.
2. Azure AD se krátce zobrazí "Přejde me to your organization's page" a uživatel je přesměrován na přihlašovací stránku poskytovatele identity. Přihlašovací stránka je obvykle přizpůsobena logem organizace.

Podporované verze systému AD FS v tomto federovaném scénáři jsou:
- Služba AD FS v2 služby Active Directory Federation Services
- Služba AD FS V3 (Windows Server 2012 R2)
- Služba AD FS v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Získání tokenu pomocí uživatelského jména a hesla

Když získáte token `ConfidentialClientApplication.AcquireToken()` pomocí `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` nebo `UsernamePasswordParameters`s nebo , MSAL pro Java získá zprostředkovatele identity kontaktovat na základě uživatelského jména. MSAL pro Java získá [token tokenu SAML 1.1](reference-saml-tokens.md) od zprostředkovatele identity, který pak poskytuje azure ad, který vrátí JSON webový token (JWT).

## <a name="next-steps"></a>Další kroky

Federovaný případ najdete v [tématu Konfigurace chování přihlášení služby Azure Active Directory pro aplikaci pomocí zásad zjišťování domovské sféry.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)