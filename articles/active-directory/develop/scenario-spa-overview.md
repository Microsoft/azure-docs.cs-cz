---
title: Přehled scénářů jednoduchých stránek JavaScriptu – platforma Microsoftu identity
description: Naučte se vytvářet jednostránkové aplikace (Přehled scénářů), která integruje platformu Microsoft Identity Platform.
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
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852520"
---
# <a name="scenario-single-page-application"></a>Scénář: Jednostránková aplikace

Naučte se všechno, co potřebujete k vytvoření jednostránkové aplikace (SPA).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Svou první aplikaci můžete vytvořit pomocí rychlého startu pro JavaScript SPA:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Jednostránkové aplikace](./quickstart-v2-javascript.md)

## <a name="overview"></a>Přehled

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta napsané pomocí JavaScriptu nebo rozhraní zabezpečeného hesla, jako je například úhlový Vue. js, a reagují. js. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru. Platforma Microsoft Identity Platform umožňuje používat jednostránkové aplikace k přihlašování uživatelů a získat tokeny pro přístup k back-endové službě nebo k webovým rozhraním API pomocí [implicitního toku OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Implicitní tok umožňuje aplikaci získat tokeny ID, které reprezentují ověřeného uživatele a také přístup k tokenům potřebným pro volání chráněných rozhraní API.

![Jednostránkové aplikace](./media/scenarios/spa-app.svg)

Tento tok ověřování nezahrnuje scénáře aplikací pomocí rozhraní JavaScript pro různé platformy, jako je například elektron, reakce-nativní a tak dále. protože vyžadují další možnosti pro interakci s nativními platformami.

## <a name="specifics"></a>Specifika

Pro povolení tohoto scénáře pro vaši aplikaci jsou potřeba následující aspekty:

* Registrace aplikace ve službě Azure AD zahrnuje povolení implicitního toku a nastavení identifikátoru URI přesměrování, ke kterému se tokeny vrátí.
* Konfigurace aplikace s registrovanými vlastnostmi aplikace, jako je například ID aplikace
* Použití knihovny MSAL k tomu, aby se tok ověřování přihlásil a získal tokeny.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-spa-app-registration.md)
