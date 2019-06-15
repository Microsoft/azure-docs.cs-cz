---
title: Přehled scénáře jednostránková aplikace JavaScript – platforma identit Microsoft
description: Zjistěte, jak sestavit jednostránkové aplikaci (Přehled scénáře), která integruje platforma identit Microsoft.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076357"
---
# <a name="scenario-single-page-application"></a>Scénář: Jednostránková aplikace

Přečtěte si všechno, co potřebujete k sestavení jednostránkové aplikace (SPA).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Vytvoření první aplikace pomocí jazyka JavaScript SPA rychlý start:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Jednostránková aplikace](./quickstart-v2-javascript.md)

## <a name="overview"></a>Přehled

Řada moderních webových aplikací jsou vytvořené jako na straně klienta jednostránkové aplikace napsané v jazyce JavaScript nebo nějaké SPA rozhraní, například Angular, Vue.js a React.js. Tyto aplikace spouštět ve webovém prohlížeči a mají různé ověřování vlastnosti než tradiční na straně serveru webové aplikace. Platforma identit Microsoft umožňuje jednostránkové aplikace přihlásit uživatele a získat tokeny pro přístup k back-endovým službám nebo webové rozhraní API pomocí [implicitního toku OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Implicitní tok umožňuje, aby aplikace k získání ID tokenů pro reprezentaci ověřeného uživatele a také přístupové tokeny, které jsou potřebné k volání rozhraní API služby chráněný.

![Jednostránkové aplikace](./media/scenarios/spa-app.svg)

Tento tok ověřování nezahrnuje scénáře aplikací pomocí architektury JavaScript napříč platformami, jako jsou elektronovým, React-Native a tak dále. od té doby se vyžadovat další možnosti pro interakci s nativní platformy.

## <a name="specifics"></a>Specifika

Následující aspekty je potřeba povolit tento scénář pro vaši aplikaci:

* Registrace aplikace ve službě Azure AD zahrnuje povolení implicitní tok a nastavení identifikátoru URI přesměrování, ke kterému tokeny jsou vráceny.
* Konfigurace aplikace s vlastnostmi zaregistrovanou aplikaci jako je například ID aplikace.
* Tok ověření pro přihlášení a získávat tokeny pomocí knihovna MSAL.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-spa-app-registration.md)
