---
title: Podpora Azure AD FS (MSAL Python)
titleSuffix: Microsoft identity platform
description: Informace o podpoře služby AD FS (Active Directory Federation Services) v knihovně Microsoft Authentication Library for Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699542"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Podpora služby AD FS v jazyce MSAL pro Python

Služba AD FS (AD FS) služby Active Directory (AD FS) v systému Windows Server umožňuje přidat ověřování a autorizaci aplikací založených na protokolu OpenID Connect a OAuth 2.0 pomocí knihovny Microsoft Authentication Library (MSAL) pro Python. Pomocí knihovny MSAL pro Python může vaše aplikace ověřovat uživatele přímo ve službě AD FS. Další informace o scénářích naleznete [v tématu AD FS Scenarios for Developers](/windows-server/identity/ad-fs/ad-fs-development).

Obvykle existují dva způsoby ověřování ve s. AD FS:

- MSAL Python mluví s Azure Active Directory, který sám je federován s jinými poskytovateli identit. Federace se děje prostřednictvím AD FS. MSAL Python se připojuje k Azure AD, který se přihlásí k uživatelům, kteří jsou spravovaní ve službě Azure AD (spravovaní uživatelé) nebo uživatelé spravovaní jiným poskytovatelem identity, jako je služba AD FS (federovaní uživatelé). MSAL Python neví, že uživatel je federovaný. Jednoduše promluví se službou Azure AD. [Autorita,](msal-client-application-configuration.md#authority) kterou v tomto případě používáte, je obvyklá autorita (název hostitele autority + tenant, běžný nebo organizace).
- MSAL Python hovoří přímo s autoritou AD FS. To je podporováno pouze AD FS 2019 a novější.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Připojení ke službě Active Directory federované službou AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Získání tokenu interaktivně pro federovaného uživatele

Následující text platí bez ohledu na to, zda se připojujete přímo ke službě AD FS (Active Directory Federation Services) nebo prostřednictvím služby Active Directory.

Při volání `acquire_token_by_authorization_code` `acquire_token_by_device_flow`nebo , uživatelské prostředí je obvykle následující:

1. Uživatel zadá ID svého účtu.
2. Azure AD krátce zobrazí zprávu "Přejde metou na stránku vaší organizace" a uživatel je přesměrován na přihlašovací stránku poskytovatele identity. Přihlašovací stránka je obvykle přizpůsobena logem organizace.

Podporované verze systému AD FS v tomto federovaném scénáři jsou:
- Služba AD FS v2 služby Active Directory Federation Services
- Služba AD FS V3 (Windows Server 2012 R2)
- Služba AD FS v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Získání tokenu pomocí uživatelského jména a hesla

Následující text platí bez ohledu na to, zda se připojujete přímo ke službě AD FS (Active Directory Federation Services) nebo prostřednictvím služby Active Directory.

Když získáte token `acquire_token_by_username_password`pomocí , MSAL Python získá zprostředkovatele identity kontaktovat na základě uživatelského jména. MSAL Python získá [token SAML 1.1](reference-saml-tokens.md) od poskytovatele identity, který pak poskytuje azure ad, který vrátí JSON webový token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Přímé připojení ke službě AD FS

Když připojíte adresář ke službě AD FS, autorita, kterou budete chtít použít k vytvoření aplikace, bude něco jako`https://somesite.contoso.com/adfs/`

MSAL Python podporuje ADFS 2019.

Nepodporuje přímé připojení k adfs 2016 nebo ADFS v2. Pokud potřebujete podporovat scénáře vyžadující přímé připojení k ADFS 2016, použijte nejnovější verzi ADAL Pythonu. Po upgradu místního systému na ADFS 2019 můžete použít MSAL Python.

## <a name="next-steps"></a>Další kroky

- Federovaný případ najdete v [tématu Konfigurace chování přihlášení služby Azure Active Directory pro aplikaci pomocí zásad zjišťování domovské sféry.](../manage-apps/configure-authentication-for-federated-users-portal.md)