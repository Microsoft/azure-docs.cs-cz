---
title: Vytvoření webového rozhraní API, které volá webová rozhraní API – Microsoft Identity Platform | Azure
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
ms.openlocfilehash: b67507daf8005f3f9a299b778f1fba4ce67d46d4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044157"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scénář: webové rozhraní API, které volá webová rozhraní API.

Zjistěte, co potřebujete vědět, abyste mohli vytvořit webové rozhraní API, které volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

Tento scénář, ve kterém chráněné webové rozhraní API volá webová rozhraní API, se sestavuje na základě scénáře "Ochrana webového rozhraní API". Další informace o tomto základních scénářích najdete v tématu [scénář: chráněné webové rozhraní API](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Přehled

- Webový klient, stolní nebo mobilní aplikace nebo klient s jednou stránkou (který není reprezentován v doprovodném diagramu) volá chráněné webové rozhraní API a v jeho autorizační hlavičce "autorizace" poskytne nosný token JSON Web Token (JWT).
- Chráněné webové rozhraní API ověří token a pomocí metody Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` požádat o jiný token od Azure Active Directory (Azure AD), aby chráněné webové rozhraní API mohlo zavolat druhé webové rozhraní API nebo podřízené webové rozhraní API jménem uživatele.
- Chráněné webové rozhraní API může také volat `AcquireTokenSilent`později, aby požádala o tokeny pro jiná rozhraní API pro příjem dat jménem stejného uživatele. v případě potřeby aktualizuje token `AcquireTokenSilent`.

![Diagram webového rozhraní API, které volá webové rozhraní API](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifika

Část registrace aplikace, která souvisí s oprávněními rozhraní API, je klasická. Konfigurace aplikace zahrnuje použití toku OAuth 2,0 za běhu za účelem výměny nosného tokenu JWT na tokenu pro rozhraní API pro příjem dat. Tento token se přidá do mezipaměti tokenů, kde je k dispozici v řadičích webového rozhraní API, a potom může získat token v tichém režimu pro volání rozhraní API pro příjem dat.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-web-api-call-api-app-registration.md)
