---
title: Podpora Azure AD FS (MSAL Python)
titleSuffix: Microsoft identity platform
description: Další informace o podpoře Active Directory Federation Services (AD FS) (AD FS) v knihovně Microsoft Authentication Library for Python
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
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 161bcb2a4276b39765361e97a4516812c26ef580
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064466"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Podpora Active Directory Federation Services (AD FS) v MSAL pro Python

Active Directory Federation Services (AD FS) (AD FS) ve Windows serveru vám umožní přidat ověřování a autorizaci na základě protokolu OAuth 2,0 pro vaše aplikace pomocí knihovny Microsoft Authentication Library (MSAL) pro Python. Pomocí MSAL pro Python Library může aplikace ověřovat uživatele přímo proti AD FS. Další informace o scénářích najdete v tématu [AD FSch scénářů pro vývojáře](/windows-server/identity/ad-fs/ad-fs-development).

Existují dva způsoby ověřování proti AD FS:

- MSAL Python mluví na Azure Active Directory, které jsou federované s ostatními zprostředkovateli identity. K Federaci dochází prostřednictvím AD FS. MSAL Python se připojuje ke službě Azure AD, která přihlašuje uživatele spravované ve službě Azure AD (spravované uživatele) nebo uživatele spravované jiným poskytovatelem identity, jako je AD FS (federované uživatele). MSAL Python neví, že je uživatel federovaný. Jednoduše mluví s Azure AD. [Autorita](msal-client-application-configuration.md#authority) , kterou použijete v tomto případě, je obvyklá autorita (název hostitele autority + tenant, Common nebo organizace).
- MSAL Python mluví přímo k AD FS autoritě. To je podporováno pouze v AD FS 2019 a novějších.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Připojení ke službě Active Directory federované pomocí AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Interaktivní získání tokenu pro federovaného uživatele

Následující postup platí, pokud se připojujete přímo k Active Directory Federation Services (AD FS) (AD FS) nebo prostřednictvím služby Active Directory.

Když zavoláte `acquire_token_by_authorization_code` nebo `acquire_token_by_device_flow` , uživatelské prostředí je obvykle následující:

1. Uživatel zadá své ID účtu.
2. Služba Azure AD stručně zobrazuje zprávu "převezme vás na stránku vaší organizace" a uživatel se přesměruje na přihlašovací stránku zprostředkovatele identity. Přihlašovací stránka se obvykle přizpůsobí s logem organizace.

Podporované verze AD FS v tomto federovaném scénáři jsou:
- Active Directory Federation Services (AD FS) FS v2
- Active Directory Federation Services (AD FS) V3 (Windows Server 2012 R2)
- Active Directory Federation Services (AD FS) v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Získání tokenu prostřednictvím uživatelského jména a hesla

Následující postup platí, pokud se připojujete přímo k Active Directory Federation Services (AD FS) (AD FS) nebo prostřednictvím služby Active Directory.

Když získáte token pomocí `acquire_token_by_username_password` , MSAL Python získá poskytovatele identity, aby kontaktoval na základě uživatelského jména. MSAL Python získá [token SAML 1,1](reference-saml-tokens.md) od poskytovatele identity, který potom poskytne službě Azure AD, která vrací JSON web token (Jwt).

## <a name="connecting-directly-to-ad-fs"></a>Přímé připojení k AD FS

Když připojíte adresář k AD FS, autorita, kterou budete chtít použít k sestavení vaší aplikace, bude vypadat nějak takto. `https://somesite.contoso.com/adfs/`

MSAL Python podporuje ADFS 2019.

Nepodporuje přímé připojení ke službě AD FS 2016 nebo ADFS v2. Pokud potřebujete podporu scénářů vyžadujících přímé připojení ke službě ADFS 2016, použijte nejnovější verzi Pythonu pro ADAL. Po upgradu místního systému na ADFS 2019 můžete použít MSAL Python.

## <a name="next-steps"></a>Další kroky

- V případě federovaného případu si přečtěte téma [Konfigurace chování přihlášení Azure Active Directory pro aplikaci pomocí zásad zjišťování domovské sféry](../manage-apps/configure-authentication-for-federated-users-portal.md) .
