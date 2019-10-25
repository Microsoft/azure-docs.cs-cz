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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64418138f9eff61cc94a57be4dc5e2625e5fdebb
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803680"
---
# <a name="scenario-protected-web-api"></a>Scénář: chráněné webové rozhraní API

V tomto scénáři vám ukážeme, jak můžete vystavit webové rozhraní API a jak ho chránit, aby přístup k rozhraní API mohli jenom ověření uživatelé. Pro použití vašeho webového rozhraní API budete chtít povolit ověřeným uživatelům jak pracovní, školní nebo osobní účty Microsoft.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifika

Tady jsou některé konkrétní informace, které potřebujete znát k ochraně webových rozhraní API:

- Registrace vaší aplikace musí zveřejnit aspoň jeden obor. Verze tokenu přijatá vaším webovým rozhraním API závisí na přihlášení cílové skupiny.
- Konfigurace kódu webového rozhraní API musí ověřit token, který se používá při volání webového rozhraní API.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-protected-web-api-app-registration.md)
