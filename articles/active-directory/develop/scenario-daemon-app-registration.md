---
title: Registrace aplikací pro daemon, které volají webová rozhraní API – platforma identit Microsoftu | Azure
description: Naučte se, jak vytvořit aplikaci pro daemon, která volá webová API – registrace aplikace
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885476"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplikace Daemon, která volá webová API – registrace aplikace

Pro aplikaci daemon, zde je to, co potřebujete vědět, když se zaregistrujete aplikaci.

## <a name="supported-account-types"></a>Podporované typy účtu

Daemon aplikace smysl pouze v tenanty Azure AD. Takže při vytváření aplikace, musíte zvolit jednu z následujících možností:

- **Účty pouze v tomto organizačním adresáři**. Tato volba je nejběžnější, protože aplikace daemonu jsou obvykle napsány vývojáři oboru (LOB).
- **Účty v libovolném organizačním adresáři**. Tuto volbu učiníte, pokud jste nevlastním zákazníkem, který svým zákazníkům poskytuje nástroj. Ke schválení budete potřebovat správce tenantů svých zákazníků.

## <a name="authentication---no-reply-uri-needed"></a>Ověřování - není nutná žádná odpověď URI

V případě, že vaše důvěrná klientská aplikace používá *pouze* tok pověření klienta, nemusí být identifikátor URI odpovědi registrován. Není potřeba pro konfiguraci nebo konstrukci aplikace. Tok pověření klienta nepoužívá.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Oprávnění rozhraní API – oprávnění aplikací a souhlas správce

Daemon aplikace může požadovat pouze oprávnění aplikace pro api (ne delegovaná oprávnění). Na stránce **oprávnění rozhraní API** pro registraci aplikace po **výběru možnosti Přidat oprávnění** a výběru rodiny rozhraní API zvolte Oprávnění **aplikace**a pak vyberte oprávnění.

![Oprávnění aplikací a souhlas správce](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Webové rozhraní API, které chcete volat, musí definovat *oprávnění aplikace (role aplikací)*, nikoli delegovaná oprávnění. Podrobnosti o tom, jak takové rozhraní API zpřístupnit, najdete v [tématu Chráněné webové rozhraní API: Registrace aplikací – když je vaše webové rozhraní API voláno aplikací daemon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Daemon aplikace vyžadují, aby správce klienta předem souhlas k aplikaci volání webovérozhraní API. Správci tenanta poskytují tento souhlas na stejné stránce **oprávnění rozhraní API** výběrem udělení **souhlasu správce pro naši *organizaci* **

Pokud jste isv budování víceklientské aplikace, měli byste si přečíst sekci [Nasazení – případ víceklientských aplikací daemon](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace Daemon – konfigurace kódu aplikace](./scenario-daemon-app-configuration.md)
