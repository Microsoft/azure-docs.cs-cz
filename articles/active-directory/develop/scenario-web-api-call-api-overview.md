---
title: Webové rozhraní API, které volá webová rozhraní API pro příjem dat (přehled) – Microsoft Identity Platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API pro příjem dat (přehled).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852504"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scénář: Webové rozhraní API, které volá webová rozhraní API

Seznamte se s tím, že potřebujete vytvořit webové rozhraní API, které volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

Tento scénář, chráněné webové rozhraní API, které volá webová rozhraní API, se staví na scénáři "chránit webové rozhraní API". Další informace o tomto základních scénářích najdete v tématu [Protected Web API – scénář](scenario-protected-web-api-overview.md) First.

## <a name="overview"></a>Přehled

- Klient (webová, desktopová, mobilní nebo jednostránková aplikace) – nepředstavuje v diagramu níže volání chráněného webového rozhraní API a poskytuje token nosiče JWT ve své autorizační hlavičce protokolu HTTP.
- Chráněné webové rozhraní API ověří token a použije metodu MSAL `AcquireTokenOnBehalfOf` k vyžádání (z Azure AD) dalšího tokenu, aby mohl sám sebe, zavolat druhé webové rozhraní API (s názvem pro příjem webového rozhraní API) jménem uživatele.
- Chráněné webové rozhraní API používá tento token k volání rozhraní API pro příjem dat. Může zavolat `AcquireTokenSilent`také později a požádat o tokeny pro jiná rozhraní API pro příjem dat (ale pořád za stejného uživatele). `AcquireTokenSilent`v případě potřeby aktualizuje token.

![Webové rozhraní API, které volá webové rozhraní API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifika

Součást registrace aplikace související s oprávněními rozhraní API je klasická. Konfigurace aplikace zahrnuje použití toku OAuth 2,0 za běhu za účelem výměny nosného tokenu JWT na tokenu pro rozhraní API pro příjem dat. Tento token se přidá do mezipaměti tokenů, kde je dostupný v řadičích webového rozhraní API, a může získat bez tichého volání rozhraní API pro příjem dat.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-api-call-api-app-registration.md)
