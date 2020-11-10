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
ms.openlocfilehash: c9cc8e6fcc796cfc7a09cf7bc1431646a6603af0
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442969"
---
# <a name="scenario-protected-web-api"></a>Scénář: chráněné webové rozhraní API

V tomto scénáři se dozvíte, jak vystavit webové rozhraní API. Naučíte se také, jak chránit webové rozhraní API, aby k němu měli přístup jenom ověření uživatelé.

Pokud chcete používat webové rozhraní API, musíte povolit ověřené uživatele s pracovními a školními účty, nebo povolit osobní účty Microsoft.

## <a name="specifics"></a>Specifika

Tady jsou konkrétní informace, které potřebujete znát k ochraně webových rozhraní API:

- Registrace vaší aplikace musí zveřejnit aspoň jeden *obor* nebo jednu *aplikační roli*.
  - Obory jsou zpřístupněny webovými rozhraními API, která jsou volána jménem uživatele.
  - Aplikační role jsou zpřístupněny webovými rozhraními API, která se nazývají aplikace démona (volá vaše webové rozhraní API svým jménem).
- Pokud vytváříte novou registraci aplikace webového rozhraní API, vyberte [verzi přístupového tokenu](reference-app-manifest.md#accesstokenacceptedversion-attribute) , kterou vaše webové rozhraní API přijalo `2` . U starších webových rozhraní API může být povolená verze tokenu `null` , ale tato hodnota omezuje cílovou skupinu pro přihlašování jenom na organizace a osobní účty Microsoft (MSA) se nepodporují.
- Konfigurace kódu pro webové rozhraní API musí ověřit token použitý při volání webového rozhraní API.
- Kód v akcích kontroleru musí ověřit role nebo obory v tokenu.

## <a name="recommended-reading"></a>Doporučené čtení

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři [Registrace aplikace](scenario-protected-web-api-app-registration.md).
