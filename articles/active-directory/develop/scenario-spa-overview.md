---
title: Přehled scénářů jednoduchých stránek JavaScriptu – platforma Microsoftu identity
description: Naučte se vytvářet jednostránkové aplikace (Přehled scénářů) pomocí platformy Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f59e8c628d7ba37aaf258541664e40e1d4a2dc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764674"
---
# <a name="scenario-single-page-application"></a>Scénář: jednostránkové aplikace

Naučte se všechno, co potřebujete k vytvoření jednostránkové aplikace (SPA).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Svou první aplikaci můžete vytvořit pomocí rychlého startu pro JavaScript SPA:

> [!div class="nextstepaction"]
> [Rychlý Start: jednostránkové aplikace](./quickstart-v2-javascript.md)

## <a name="overview"></a>Přehled

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta. Vývojáři si je zapisují pomocí JavaScriptu nebo architektury SPA, jako je například úhlové Vue. js, a reagují. js. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru. 

Platforma Microsoft Identity Platform umožňuje používat jednostránkové aplikace k přihlašování uživatelů a získat tokeny pro přístup k back-endové službě nebo webovým rozhraním API pomocí [implicitního toku OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Implicitní tok umožňuje aplikaci získat tokeny ID, které reprezentují ověřeného uživatele a také přístup k tokenům potřebným pro volání chráněných rozhraní API.

![Jednostránkové aplikace](./media/scenarios/spa-app.svg)

Tento tok ověřování nezahrnuje scénáře aplikací, které používají rozhraní JavaScript pro různé platformy, jako jsou například elektronicky a reagují – nativní. Vyžadují další možnosti pro interakci s nativními platformami.

## <a name="specifics"></a>Specifika

Chcete-li povolit tento scénář pro vaši aplikaci, budete potřebovat:

* Registrace aplikace s Azure Active Directory (Azure AD). Tato registrace zahrnuje povolení implicitního toku a nastavení identifikátoru URI přesměrování, ke kterému se tokeny vrátí.
* Konfigurace aplikace s registrovanými vlastnostmi aplikace, jako je například ID aplikace
* Použití knihovny Microsoft Authentication Library (MSAL) k tomu, aby tok ověřování se přihlásil a získal tokeny.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-spa-app-registration.md)
