---
title: Chráněné webové rozhraní API – přehled | Azure
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
ms.openlocfilehash: 02bd4b84cc7542714f6db45c12c4b5b13a7fb449
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852586"
---
# <a name="scenario-protected-web-api"></a>Scénář: Chráněné webové rozhraní API

V tomto scénáři vám ukážeme, jak můžete vystavit webové rozhraní API a jak ho chránit, aby přístup k rozhraní API mohli jenom ověření uživatelé. Pro použití vašeho webového rozhraní API budete chtít povolit ověřeným uživatelům jak pracovní, školní nebo osobní účty Microsoft.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifika

Tady jsou některé konkrétní informace, které potřebujete znát k ochraně webových rozhraní API:

- Registrace vaší aplikace musí zveřejnit aspoň jeden obor. Verze tokenu přijatá vaším webovým rozhraním API závisí na přihlášení cílové skupiny.
- Konfigurace kódu webového rozhraní API musí ověřit token, který se používá při volání webového rozhraní API.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-protected-web-api-app-registration.md)
