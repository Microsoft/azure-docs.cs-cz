---
title: Vytvoření webového rozhraní API, které volá webová rozhraní API – platforma identit Microsoftu | Azure
description: Zjistěte, jak vytvořit webové rozhraní API, které volá podřízené webové rozhraní API (přehled).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701735"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scénář: Webové rozhraní API, které volá webová rozhraní API

Zjistěte, co potřebujete vědět k vytvoření webového rozhraní API, které volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

Tento scénář, ve kterém chráněné webové rozhraní API volá webová rozhraní API, staví na scénáři "Chránit webové rozhraní API". Další informace o tomto základním scénáři najdete [v tématu Scénář: Chráněné webové rozhraní API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Přehled

- Webový, desktopový, mobilní nebo jednostránkový aplikační klient (není reprezentován v doprovodném diagramu) volá chráněné webové rozhraní API a poskytuje token nosiče JSON Web Token (JWT) v jeho hlavičce HTTP autorizace.
- Chráněné webové rozhraní API ověří token a použije metodu `AcquireTokenOnBehalfOf` Microsoft Authentication Library (MSAL) k vyžádání jiného tokenu ze služby Azure Active Directory (Azure AD), aby chráněné webové rozhraní API mohlo jménem uživatele volat druhé webové rozhraní API nebo rozhraní API pro příjem dat.
- Chráněné webové rozhraní API `AcquireTokenSilent`můžete také volat později požadovat tokeny pro jiné podřízené rozhraní API jménem stejného uživatele. `AcquireTokenSilent`v případě potřeby aktualizuje token.

![Diagram webového rozhraní API volajícího webové rozhraní API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifika

Část registrace aplikace, která souvisí s oprávněními rozhraní API, je klasická. Konfigurace aplikace zahrnuje použití OAuth 2.0 On-Behalf-Of toku k výměně tokenu nosiče JWT proti tokenu pro příjem rozhraní API. Tento token je přidán do mezipaměti tokenu, kde je k dispozici v řadičích webového rozhraní API a pak může získat token tiše volat rozhraní API pro příjem dat.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-api-call-api-app-registration.md)
