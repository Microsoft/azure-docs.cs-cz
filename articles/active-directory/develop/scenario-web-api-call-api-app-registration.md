---
title: Registrace webového rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API pro příjem dat (registrace aplikací).
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
ms.openlocfilehash: af1047c5f890b1b88ae6d043a30704e84b8dc079
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584310"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Webové rozhraní API, které volá webová rozhraní API: registrace aplikace

Webové rozhraní API, které volá webové rozhraní API pro příjem dat, má stejnou registraci jako chráněné webové rozhraní API. Postupujte podle pokynů v tématu [Protected Web API: registrace aplikace](scenario-protected-web-api-app-registration.md).

Vzhledem k tomu, že webová aplikace teď volá webová rozhraní API, bude se jednat o důvěrnou klientskou aplikaci. To je důvod, proč jsou požadovány další registrační informace: aplikace musí sdílet tajné klíče (pověření klienta) s platformou Microsoft identity.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění rozhraní API

Webové aplikace volají rozhraní API jménem uživatelů, pro které byl přijat token nosiče. Webové aplikace potřebují požádat o delegovaná oprávnění. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovému rozhraní API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Konfigurace kódu aplikace](scenario-web-api-call-api-app-configuration.md).
