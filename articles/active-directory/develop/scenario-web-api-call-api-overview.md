---
title: Webové rozhraní API, která volá podřízené webová rozhraní API (přehled) – platforma identit Microsoft
description: Zjistěte, jak vytvořit webové rozhraní API tohoto volání podřízené webové rozhraní API (přehled).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075397"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scénář: Webové rozhraní API, která volá webové rozhraní API

Přečtěte si všechno, co potřebujete k vytvoření webového rozhraní API, která volá webové rozhraní API.

## <a name="prerequisites"></a>Požadavky

Tento scénář, chráněné webové rozhraní API, že volání webových rozhraní API, postavená na scénář "Chránil webové rozhraní API". Další informace o tomto scénáři základní najdete v tématu [chráněné webové rozhraní API – scénář](scenario-protected-web-api-overview.md) první.

## <a name="overview"></a>Přehled

- Klient (web, stolní, mobilní nebo jednostránkové aplikace) – není znázorněné v následujícím diagramu - volá chráněné webové rozhraní API a poskytuje nosný token JWT v hlavičce Http "Autorizace".
- Ověří token chráněné webové rozhraní API a používá knihovna MSAL `AcquireTokenOnBehalfOf` volání metody k vyžádání (z Azure AD) jiný token tak, aby je možné, samostatně, druhý webového rozhraní API (s názvem podřízené webové rozhraní API) jménem uživatele.
- Chráněné webové rozhraní API používá tento token volat podřízené rozhraní API. Můžete také volat `AcquireTokenSilent`novější požadovat tokeny pro ostatní rozhraní API pro příjem dat (ale pořád jménem stejného uživatele). `AcquireTokenSilent` aktualizuje token v případě potřeby.

![Webové rozhraní API volat webové rozhraní API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifika

Součástí registrace aplikací souvisejících s oprávněními rozhraní API je klasický. Konfigurace aplikace je použít tok OAuth 2.0 on-behalf-of k výměně nosného tokenu JWT proti token pro příjem dat rozhraní API. Tento token se přidá do tokenu mezipaměti, kde je k dispozici v kontrolerů webového rozhraní API a můžete získat token tiše k volání rozhraní API pro příjem.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-api-call-api-app-registration.md)
