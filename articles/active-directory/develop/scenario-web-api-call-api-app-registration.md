---
title: Webové rozhraní API tohoto volání podřízené webové rozhraní API (registrace aplikace) – platforma identit Microsoft
description: Další informace o vytváření webového rozhraní API tohoto volání podřízené webové rozhraní API (registrace aplikace)
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075382"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Webové rozhraní API, že volání webového rozhraní API – registrace aplikace

Webové rozhraní API, která volá podřízené webové rozhraní API má stejné registraci jako chráněné webové rozhraní API. Proto budete muset postupovat podle pokynů v [chráněné webové rozhraní API – registrace aplikace](scenario-protected-web-api-app-registration.md).

Však od webové aplikace teď volá webové rozhraní API, stane se aplikace důvěrnému klientovi. To je důvod, proč je navíc registrace – informace, které je nutné: aplikace je potřeba sdílet s platformou identity Microsoft tajné kódy (přihlašovací údaje klienta).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění k rozhraní API

Webové aplikace volat rozhraní API jménem uživatele, pro kterého byla přijata nosný token. Budou muset požadovat delegovaná oprávnění. Podrobnosti najdete v tématu [přidat oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-api-call-api-app-configuration.md)
