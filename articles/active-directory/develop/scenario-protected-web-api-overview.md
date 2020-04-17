---
title: Chráněné webové rozhraní API - přehled
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit chráněné webové rozhraní API (přehled).
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537198"
---
# <a name="scenario-protected-web-api"></a>Scénář: Chráněné webové rozhraní API

V tomto scénáři se dozvíte, jak vystavit webové rozhraní API. Dozvíte se také, jak chránit webové rozhraní API tak, aby k němu měli přístup pouze ověření uživatelé.

Chcete-li používat webové rozhraní API, musíte buď povolit ověřené uživatele s pracovními i školními účty, nebo povolit osobní účty Microsoft.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifika

Zde jsou konkrétní informace, které potřebujete vědět k ochraně webových api:

- Registrace aplikace musí vystavit alespoň jeden obor. Verze tokenu přijatá vaším webovým rozhraním API závisí na přihlašovací matné skupině.
- Konfigurace kódu pro webové rozhraní API musí ověřit token používaný při volání webového rozhraní API.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikací](scenario-protected-web-api-app-registration.md)
