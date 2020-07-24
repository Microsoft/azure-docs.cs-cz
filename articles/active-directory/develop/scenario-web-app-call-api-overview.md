---
title: Sestavení webové aplikace, která volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Informace o tom, jak vytvořit webovou aplikaci, která volá webová rozhraní API (přehled)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0c4bef25a0be12c0335a82345ec998274f1faa67
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058396"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scénář: webová aplikace, která volá webová rozhraní API.

Naučte se, jak vytvořit webovou aplikaci, která uživatele podepisuje na platformě Microsoft identity, a pak zavolá webová rozhraní API jménem přihlášeného uživatele.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

V tomto scénáři se předpokládá, že jste už prošli v následujícím scénáři:

> [!div class="nextstepaction"]
> [Webová aplikace, která přihlašuje uživatele](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Přehled

Do své webové aplikace můžete přidat ověřování, aby mohli podepisovat uživatele a volat webové rozhraní API jménem přihlášeného uživatele.

![Webová aplikace, která volá webová rozhraní API](./media/scenario-webapp/web-app.svg)

Webové aplikace, které volají webová rozhraní API, jsou důvěrné klientské aplikace.
To je důvod, proč si zaregistrovali tajný klíč (heslo aplikace nebo certifikát) s Azure Active Directory (Azure AD). Tento tajný klíč se předává během volání služby Azure AD za účelem získání tokenu.

## <a name="specifics"></a>Specifika

> [!NOTE]
> Přidáním přihlášení do webové aplikace se dozvíte o ochraně samotné webové aplikace. Tato ochrana se dosahuje pomocí knihoven *middlewaru* , nikoli knihovny Microsoft Authentication Library (MSAL). Předchozí scénář – [Webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md), na kterou se vztahuje.
>
> Tento scénář popisuje, jak volat webová rozhraní API z webové aplikace. Pro tato webová rozhraní API musíte získat přístupové tokeny. K získání těchto tokenů použijete knihovny MSAL.

Vývoj pro tento scénář zahrnuje tyto konkrétní úkoly:

- Během [Registrace aplikace](scenario-web-app-call-api-app-registration.md)musíte zadat identifikátor URI, tajný klíč nebo certifikát odpovědi, které se mají sdílet se službou Azure AD. Pokud nasadíte aplikaci do několika umístění, poskytnete identifikátor URI odpovědi pro každé umístění.
- [Konfigurace aplikace](scenario-web-app-call-api-app-configuration.md) musí poskytnout přihlašovací údaje klienta, které byly sdíleny se službou Azure AD během registrace aplikace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová rozhraní API: registrace aplikace](scenario-web-app-call-api-app-registration.md)
