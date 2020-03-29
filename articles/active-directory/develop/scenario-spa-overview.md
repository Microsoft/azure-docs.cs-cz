---
title: JavaScript jednostránkový scénář aplikace - platforma identit Microsoft | Azure
description: Zjistěte, jak vytvořit jednostránkovou aplikaci (přehled scénářů) pomocí platformy identit Microsoftu.
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
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701871"
---
# <a name="scenario-single-page-application"></a>Scénář: Jednostránková aplikace

Naučte se vše, co potřebujete k vytvoření jednostránkové aplikace (SPA).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

První aplikaci můžete vytvořit pomocí rychlého startu JavaScript SPA:

> [!div class="nextstepaction"]
> [Úvodní příručka: Jednostránková aplikace](./quickstart-v2-javascript.md)

## <a name="overview"></a>Přehled

Mnoho moderních webových aplikací je vytvořeno jako jednostránkové aplikace na straně klienta. Vývojáři je píší pomocí JavaScriptu nebo spa frameworku, například Angular, Vue.js a React.js. Tyto aplikace běží ve webovém prohlížeči a mají jiné charakteristiky ověřování než tradiční webové aplikace na straně serveru. 

Platforma identit společnosti Microsoft umožňuje jednostránkovým aplikacím přihlašovat uživatele a získat tokeny pro přístup k back-endovým službám nebo webovým rozhraním API pomocí [implicitního toku OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Implicitní tok umožňuje aplikaci získat ID tokeny reprezentovat ověřeného uživatele a také přístup tokeny potřebné k volání chráněných rozhraní API.

![Jednostránkové aplikace](./media/scenarios/spa-app.svg)

Tento tok ověřování nezahrnuje scénáře aplikací, které používají architektury JavaScript mezi platformami, jako jsou Electron a React-Native. Vyžadují další možnosti pro interakci s nativníplatformy.

## <a name="specifics"></a>Specifika

Chcete-li povolit tento scénář pro vaši aplikaci, potřebujete:

* Registrace aplikace pomocí Služby Azure Active Directory (Azure AD). Tato registrace zahrnuje povolení implicitní tok a nastavení přesměrování URI, do kterého jsou vráceny tokeny.
* Konfigurace aplikace s registrovanými vlastnostmi aplikace, jako je například ID aplikace.
* Pomocí knihovny Microsoft Authentication Library (MSAL) proveďte tok ověřování pro přihlášení a získání tokenů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-spa-app-registration.md)
