---
title: Registrace webového rozhraní API, které volá webová rozhraní API – platforma identit microsoftu | Azure
description: Zjistěte, jak vytvořit webové rozhraní API, které volá podřízené webové rozhraní API (registrace aplikací).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885119"
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
