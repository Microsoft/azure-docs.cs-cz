---
title: Sestavení webového rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API pro příjem dat (přehled).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a66f0a2de1d8239baffbe53dfb5d6f2dd275d448
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756344"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scénář: webové rozhraní API, které volá webová rozhraní API.

Zjistěte, co potřebujete vědět, abyste mohli vytvořit webové rozhraní API, které volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

Tento scénář, ve kterém chráněné webové rozhraní API volá jiná webová rozhraní API, se vystaví ve [scénáři: chráněné webové rozhraní API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Přehled

- Webový klient, stolní nebo mobilní aplikace nebo klient s jednou stránkou (který není reprezentován v doprovodném diagramu) volá chráněné webové rozhraní API a v jeho autorizační hlavičce "autorizace" poskytne nosný token JSON Web Token (JWT).
- Chráněné webové rozhraní API ověří token a pomocí metody Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` požádá o jiný token z Azure Active Directory (Azure AD), aby chráněné webové rozhraní API mohlo zavolat druhé webové rozhraní API nebo podřízené webové rozhraní API jménem uživatele.
- Chráněné webové rozhraní API může zavolat také `AcquireTokenSilent` později a požádat o tokeny pro jiná rozhraní API pro příjem dat jménem stejného uživatele. `AcquireTokenSilent` v případě potřeby aktualizuje token.

![Diagram webového rozhraní API, které volá webové rozhraní API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifika

Část registrace aplikace, která souvisí s oprávněními rozhraní API, je klasická. Konfigurace aplikace zahrnuje použití toku OAuth 2,0 za běhu za účelem výměny nosného tokenu JWT na tokenu pro rozhraní API pro příjem dat. Tento token se přidá do mezipaměti tokenů, kde je k dispozici v řadičích webového rozhraní API, a potom může získat token v tichém režimu pro volání rozhraní API pro příjem dat.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři [Registrace aplikace](scenario-web-api-call-api-app-registration.md).
