---
title: Registrace webového rozhraní API, které volá webová rozhraní API – platforma identit microsoftu | Azure
description: Zjistěte, jak vytvořit webové rozhraní API, které volá podřízené webové rozhraní API (registrace aplikací).
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701786"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Webové rozhraní API, které volá webová rozhraní API: Registrace aplikací

Webové rozhraní API, které volá podřízené webové rozhraní API, má stejnou registraci jako chráněné webové rozhraní API. Proto je třeba postupovat podle pokynů v [chráněné webové rozhraní API: Registrace aplikací](scenario-protected-web-api-app-registration.md).

Vzhledem k tomu, že webová aplikace nyní volá webová api, stane se důvěrnou klientskou aplikací. To je důvod, proč jsou vyžadovány další registrační informace: aplikace potřebuje sdílet tajné klíče (přihlašovací údaje klienta) s platformou identit microsoftu.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění rozhraní API

Webové aplikace volají api jménem uživatelů, pro které byl přijat token nosiče. Webové aplikace musí požádat o delegovaná oprávnění. Další informace naleznete v tématu [Přidání oprávnění k přístupu k webovým sítím API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Konfigurace kódu](scenario-web-api-call-api-app-configuration.md)
