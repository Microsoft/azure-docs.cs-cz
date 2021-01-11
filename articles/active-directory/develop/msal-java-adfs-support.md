---
title: Podpora AD FS (MSAL for Java)
titleSuffix: Microsoft identity platform
description: Přečtěte si o podpoře Active Directory Federation Services (AD FS) (AD FS) v knihovně Microsoft Authentication Library for Java (MSAL4j).
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
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 82e3f3b32c6d96b83ec1d0402f344e7d65788c06
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063701"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Podpora Active Directory Federation Services (AD FS) v MSAL pro Java

Active Directory Federation Services (AD FS) (AD FS) ve Windows serveru vám umožní přidat ověřování a autorizaci na základě protokolu OAuth 2,0 pro aplikaci Microsoft Authentication Library pro Java (MSAL for Java). Po integraci aplikace může ověřit uživatele v AD FS federované prostřednictvím služby Azure AD. Další informace o scénářích najdete v tématu [AD FSch scénářů pro vývojáře](/windows-server/identity/ad-fs/ad-fs-development).

Aplikace, která používá MSAL pro Java, bude komunikovat s Azure Active Directory (Azure AD), která pak federuje na AD FS.

MSAL for Java se připojuje ke službě Azure AD, která přihlašuje uživatele spravované v Azure AD (spravované uživatele) nebo uživatele spravované jiným poskytovatelem identity, jako je AD FS (federované uživatele). MSAL for Java neví, že je uživatel federovaný. Jednoduše mluví s Azure AD.

[Autorita](msal-client-application-configuration.md#authority) , kterou použijete v tomto případě, je obvyklá autorita (název hostitele autority + tenant, Common nebo organizace).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Interaktivní získání tokenu pro federovaného uživatele

Když zavoláte `ConfidentialClientApplication.AcquireToken()` nebo `PublicClientApplication.AcquireToken()` s `AuthorizationCodeParameters` nebo `DeviceCodeParameters` , uživatelské prostředí je obvykle:

1. Uživatel zadá své ID účtu.
2. Azure AD krátce zobrazuje "přebírá vás na stránku vaší organizace" a uživatel se přesměruje na přihlašovací stránku zprostředkovatele identity. Přihlašovací stránka se obvykle přizpůsobí s logem organizace.

Podporované verze AD FS v tomto federovaném scénáři jsou:
- Active Directory Federation Services (AD FS) FS v2
- Active Directory Federation Services (AD FS) V3 (Windows Server 2012 R2)
- Active Directory Federation Services (AD FS) v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Získání tokenu prostřednictvím uživatelského jména a hesla

Když získáte token pomocí nebo `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` s `IntegratedWindowsAuthenticationParameters` nebo `UsernamePasswordParameters` , MSAL pro jazyk Java získá poskytovatele identity, aby kontaktoval na základě uživatelského jména. MSAL for Java získá token [tokenu SAML 1,1](reference-saml-tokens.md) od poskytovatele identity, který potom poskytne službě Azure AD, která vrací JSON web token (Jwt).

## <a name="next-steps"></a>Další kroky

V případě federovaného případu si přečtěte téma [Konfigurace chování přihlášení Azure Active Directory pro aplikaci pomocí zásad zjišťování domovské sféry](../manage-apps/configure-authentication-for-federated-users-portal.md) .