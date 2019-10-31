---
title: Aplikace démona, která volá webová rozhraní API (registrace aplikací) – platforma Microsoftu identity
description: Informace o tom, jak vytvořit aplikaci démona, která volá webová rozhraní API – registrace aplikace
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d0d88f72cca45280bd76ac7bb9d7a6e0a1d37fb
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175444"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplikace démona, která volá webová rozhraní API – registrace aplikace

V případě aplikace démona je zde uvedeno, co potřebujete znát při registraci aplikace.

## <a name="supported-account-types"></a>Podporované typy účtu

Vzhledem k tom, že aplikace démona dávají smysl jenom v klientech Azure AD, musíte při vytváření aplikace zvolit tyto možnosti:

- buď **účty v tomto organizačním adresáři**. Tato volba je nejčastějším případem, protože aplikace démona jsou obvykle zapisovány obchodními vývojáři (LOB).
- **účty nebo v jakémkoli organizačním adresáři**. Tuto možnost můžete vybrat, pokud jste nezávislý výrobce softwaru, který zákazníkům poskytuje nástroj pro nástroj. K schválení budete potřebovat správce tenantů pro zákazníky.

## <a name="authentication---no-reply-uri-needed"></a>Ověřování-není potřeba žádný identifikátor URI pro odpověď

V případě, že vaše důvěrná klientská aplikace používá **pouze** tok přihlašovacích údajů klienta, identifikátor URI odpovědi nemusí být zaregistrován. Není nutné ani pro konfiguraci a konstrukci aplikace. Tok přihlašovacích údajů klienta ho nepoužívá.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Oprávnění rozhraní API – oprávnění aplikace a souhlas správce

Aplikace démona může požadovat oprávnění aplikace jenom pro rozhraní API (nejsou delegovaná oprávnění). Na stránce **oprávnění rozhraní API** pro registraci aplikace po výběru možnosti **Přidat oprávnění** a zvolení rodiny rozhraní API zvolte **oprávnění aplikace**a pak vyberte vaše oprávnění.

![Oprávnění aplikace a souhlas správce](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Webové rozhraní API, které chcete volat, musí definovat **oprávnění aplikace (aplikační role)** , ne delegovaná oprávnění. Podrobnosti o tom, jak vystavit takové rozhraní API, najdete v tématu [Protected Web API: registrace aplikace – když je webové rozhraní API voláno aplikací démona.](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)

Aplikace démona vyžadují, aby aplikace, která volá webové rozhraní API, měla předem souhlas správce tenanta. Tento souhlas je k dispozici na stejné stránce **oprávnění API** správcem tenanta, který v  ***naší organizaci* vybere udělení souhlasu správce** .

Pokud jste nezávislý výrobce softwaru, který vytváří víceklientské aplikace, měli byste se podívat na [část nasazení v případě aplikace démona s více klienty](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) .

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace démon – konfigurace kódu aplikace](./scenario-daemon-app-configuration.md)
