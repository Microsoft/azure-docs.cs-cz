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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537198"
---
# <a name="scenario-protected-web-api"></a>Scénář: chráněné webové rozhraní API

V tomto scénáři se dozvíte, jak vystavit webové rozhraní API. Naučíte se také, jak chránit webové rozhraní API, aby k němu měli přístup jenom ověření uživatelé.

Pokud chcete používat webové rozhraní API, musíte povolit ověřené uživatele s pracovními a školními účty, nebo povolit osobní účty Microsoft.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifika

Tady jsou konkrétní informace, které potřebujete znát k ochraně webových rozhraní API:

- Registrace vaší aplikace musí zveřejnit aspoň jeden obor. Verze tokenu přijatá webovým rozhraním API závisí na tom, jakou cílovou skupinu přihlašujete.
- Konfigurace kódu pro webové rozhraní API musí ověřit token použitý při volání webového rozhraní API.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikací](scenario-protected-web-api-app-registration.md)
