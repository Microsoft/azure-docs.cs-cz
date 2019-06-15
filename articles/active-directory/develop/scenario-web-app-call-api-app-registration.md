---
title: Webové aplikace, že volání webových rozhraní API (registrace aplikace) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, že volání webových rozhraní API (registrace aplikace)
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075187"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Webové aplikace, že volání webového rozhraní API – registrace aplikace

Webové aplikace volání webového rozhraní API má stejné registraci jako webovou aplikaci přihlášení uživatele. Proto budete muset postupovat podle pokynů v [webová aplikace, které přihlásí uživatelé – registrace aplikace](scenario-web-app-sign-user-app-registration.md)

Ale od webové aplikace teď volá webové rozhraní API, stane se aplikace důvěrnému klientovi. To je důvod, proč je hodně navíc je nutná registrace: je potřeba sdílet s platformou identity Microsoft tajné kódy (přihlašovací údaje klienta).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění k rozhraní API

Webové aplikace volat rozhraní API jménem přihlášeného uživatele. Budou muset požadovat delegovaná oprávnění. Podrobnosti najdete v tématu [přidat oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-app-call-api-app-configuration.md)
