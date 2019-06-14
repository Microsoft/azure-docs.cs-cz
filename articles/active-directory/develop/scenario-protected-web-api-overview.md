---
title: Chráněné webové rozhraní API – přehled | Azure
description: Zjistěte, jak sestavit chráněné webové rozhraní API (přehled).
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
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074887"
---
# <a name="scenario-protected-web-api"></a>Scénář: Chráněné webové rozhraní API

V tomto scénáři vám ukážeme, jak můžete zpřístupnit webového rozhraní API a jak ho můžete chránit tak, aby jenom ověření uživatelé můžou k rozhraní API. Budete chtít povolit ověřené uživatele s pracovní a školní účty nebo osobní osobní účty Microsoft používat vaše webové rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifika

Tady jsou některé specifika potřebujete vědět k ochraně webového rozhraní API:

- Registrace vaší aplikace musí zveřejnit aspoň jeden obor. Token verze přijal vaše webové rozhraní API závisí na znak v cílové skupině.
- Konfigurace kódu pro webové rozhraní API musí ověřit token, který se používá při volání webového rozhraní API.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-protected-web-api-app-registration.md)
