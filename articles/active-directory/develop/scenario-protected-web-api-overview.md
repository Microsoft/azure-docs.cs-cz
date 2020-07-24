---
title: Chráněné webové rozhraní API – přehled
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API (přehled).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4e530f76c8301dc74f73b675befa6f0710aedab7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026624"
---
# <a name="scenario-protected-web-api"></a>Scénář: chráněné webové rozhraní API

V tomto scénáři se dozvíte, jak vystavit webové rozhraní API. Naučíte se také, jak chránit webové rozhraní API, aby k němu měli přístup jenom ověření uživatelé.

Pokud chcete používat webové rozhraní API, musíte povolit ověřené uživatele s pracovními a školními účty, nebo povolit osobní účty Microsoft.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifika

Tady jsou konkrétní informace, které potřebujete znát k ochraně webových rozhraní API:

- Registrace vaší aplikace musí zveřejnit aspoň jeden *obor* nebo jednu *aplikační roli*.
  - Obory jsou zpřístupněny webovými rozhraními API, která jsou volána jménem uživatele.
  - Aplikační role jsou zpřístupněny webovými rozhraními API, která se nazývají aplikace démona (volá vaše webové rozhraní API svým jménem).
- Pokud vytváříte novou registraci aplikace webového rozhraní API, vyberte [verzi přístupového tokenu](reference-app-manifest.md#accesstokenacceptedversion-attribute) , kterou vaše webové rozhraní API přijalo `2` . U starších webových rozhraní API může být povolená verze tokenu `null` , ale tato hodnota omezuje cílovou skupinu pro přihlašování jenom na organizace a osobní účty Microsoft (MSA) se nepodporují.
- Konfigurace kódu pro webové rozhraní API musí ověřit token použitý při volání webového rozhraní API.
- Kód v akcích kontroleru musí ověřit role nebo obory v tokenu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikací](scenario-protected-web-api-app-registration.md)
