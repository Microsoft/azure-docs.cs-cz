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
ms.openlocfilehash: cb5f4763e13935b99564bfcb6d8b6e7f463ed59e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919796"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Webové rozhraní API, které volá webová rozhraní API – registrace aplikace

Webové rozhraní API, které volá webové rozhraní API pro příjem dat, má stejnou registraci jako chráněné webové rozhraní API. Proto budete muset postupovat podle pokynů v tématu [Protected Web API – registrace aplikace](scenario-protected-web-api-app-registration.md).

Vzhledem k tomu, že webová aplikace teď volá webová rozhraní API, se z nich stala důvěrná klientská aplikace. To je důvod, proč jsou požadovány další informace o registraci: aplikace musí sdílet tajné klíče (pověření klienta) s platformou Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění rozhraní API

Webové aplikace volají rozhraní API jménem uživatele, pro kterého byl přijat token nosiče. Potřebují požádat o delegovaná oprávnění. Podrobnosti najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-web-api-call-api-app-configuration.md)
