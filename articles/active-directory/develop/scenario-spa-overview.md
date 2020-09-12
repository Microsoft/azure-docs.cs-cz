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
ms.openlocfilehash: 60e4ca80faa2c8787a13d87ab06cad9243299e50
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291942"
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

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta. Vývojáři si je zapisují pomocí JavaScriptu nebo architektury SPA, jako je například úhlová, Vue a reakce. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru. 

Platforma Microsoft Identity Platform nabízí **dvě** možnosti, jak přihlašovat jednostránkové aplikace uživatelům a získat tokeny pro přístup k back-endové službě nebo webovým rozhraním API:

- [Tok autorizačního kódu OAuth 2,0 (s PKCE)](./v2-oauth2-auth-code-flow.md). Tok autorizačního kódu umožňuje aplikaci výměnu autorizačního kódu pro tokeny **ID** , které reprezentují ověřeného uživatele a **přístupové** tokeny potřebné pro volání chráněných rozhraní API. Kromě toho vrací **aktualizační** tokeny, které poskytují dlouhodobý přístup k prostředkům jménem uživatelů bez nutnosti interakce s těmito uživateli. Toto je **doporučený** postup.

![Jednostránkové aplikace – ověřování](./media/scenarios/spa-app-auth.svg)

- [Implicitní tok OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Tok implicitního udělení umožňuje aplikaci získat **ID** a **přístupové** tokeny. Na rozdíl od toku autorizačního kódu nevrátí tok implicitního udělení **obnovovací token**.

![Jednostránkové aplikace – implicitní](./media/scenarios/spa-app.svg)

Tento tok ověřování nezahrnuje scénáře aplikací, které používají rozhraní JavaScript pro různé platformy, jako jsou například elektronicky a reagují – nativní. Vyžadují další možnosti pro interakci s nativními platformami.

## <a name="specifics"></a>Specifika

Chcete-li povolit tento scénář pro vaši aplikaci, budete potřebovat:

* Registrace aplikace s Azure Active Directory (Azure AD). Registrační postup se liší mezi implicitním tokem udělení a tokem autorizačního kódu.
* Konfigurace aplikace s registrovanými vlastnostmi aplikace, jako je ID aplikace
* Použití knihovny ověřování Microsoft pro JavaScript (MSAL.js) k tomu, aby tok ověřování se přihlásil a získal tokeny.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-spa-app-registration.md)
