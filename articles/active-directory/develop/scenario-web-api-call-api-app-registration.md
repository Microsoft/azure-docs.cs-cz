---
title: Registrace webového rozhraní API, které volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API pro příjem dat (registrace aplikací).
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
ms.openlocfilehash: 011007b0a871052c89e8271f2b75168e314a3c72
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044210"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Webové rozhraní API, které volá webová rozhraní API: registrace aplikace

Webové rozhraní API, které volá webové rozhraní API pro příjem dat, má stejnou registraci jako chráněné webové rozhraní API. Proto je nutné postupovat podle pokynů v tématu [Protected Web API: registrace aplikace](scenario-protected-web-api-app-registration.md).

Vzhledem k tomu, že webová aplikace teď volá webová rozhraní API, bude se jednat o důvěrnou klientskou aplikaci. To je důvod, proč jsou požadovány další registrační informace: aplikace musí sdílet tajné klíče (pověření klienta) s platformou Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění rozhraní API

Webové aplikace volají rozhraní API jménem uživatelů, pro které byl přijat token nosiče. Webové aplikace potřebují požádat o delegovaná oprávnění. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Konfigurace kódu](scenario-web-api-call-api-app-configuration.md)
