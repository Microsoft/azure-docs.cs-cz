---
title: Registrace webové aplikace, která volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Zjistěte, jak zaregistrovat webovou aplikaci, která volá webová rozhraní API.
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
ms.openlocfilehash: bcb4fa5eb5889698174b65eb576c9caf0c82f857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701667"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Webová aplikace, která volá webová rozhraní API – registrace aplikace

Webová aplikace, která volá webová rozhraní API, má stejnou registraci jako přihlašování uživatelů webové aplikace. Proto budete muset postupovat podle pokynů ve [webové aplikaci, které přihlásí uživatele – registrace aplikace](scenario-web-app-sign-user-app-registration.md) .

Vzhledem k tomu, že webová aplikace teď volá webová rozhraní API, se z nich stala důvěrná klientská aplikace. To je důvod, proč je vyžadována bitová kopie dodatečné registrace: musí sdílet tajné klíče (pověření klienta) s platformou Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění rozhraní API

Webové aplikace volají rozhraní API jménem přihlášeného uživatele. Potřebují požádat o delegovaná oprávnění. Podrobnosti najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-app-call-api-app-configuration.md)
