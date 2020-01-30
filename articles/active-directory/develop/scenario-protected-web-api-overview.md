---
title: Chráněné webové rozhraní API – přehled
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API (přehled).
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
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773342"
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
> [Registrace aplikace](scenario-protected-web-api-app-registration.md)
