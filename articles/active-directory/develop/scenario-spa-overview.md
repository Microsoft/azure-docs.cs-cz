---
title: Scénář jednostránkové aplikace JavaScriptu – Microsoft Identity Platform | Azure
description: Naučte se vytvářet jednostránkové aplikace (Přehled scénářů) pomocí platformy Microsoft Identity Platform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885170"
---
# <a name="scenario-single-page-application"></a>Scénář: jednostránkové aplikace

Naučte se všechno, co potřebujete k vytvoření jednostránkové aplikace (SPA).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Svou první aplikaci můžete vytvořit pomocí rychlého startu pro JavaScript SPA:

> [!div class="nextstepaction"]
> [Rychlý Start: jednostránkové aplikace](./quickstart-v2-javascript.md)

## <a name="overview"></a>Přehled

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta. Vývojáři si je zapisují pomocí JavaScriptu nebo architektury SPA, jako je například úhlové, Vue.js a React.js. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru. 

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
