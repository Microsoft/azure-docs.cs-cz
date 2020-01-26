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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759053"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Webová aplikace, která volá webová rozhraní API: registrace aplikace

Webová aplikace, která volá webová rozhraní API, má stejnou registraci jako webová aplikace, která uživatele podepisuje. Postupujte proto podle pokynů ve [webové aplikaci, která se přihlásí uživatelům: registrace aplikace](scenario-web-app-sign-user-app-registration.md).

Vzhledem k tomu, že webová aplikace teď také volá webová rozhraní API, se z nich stala důvěrná klientská aplikace. To je důvod, proč je nutné provést další registraci. Aplikace musí sdílet přihlašovací údaje klienta nebo *tajné klíče*s platformou Microsoft identity.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění API

Webové aplikace volají rozhraní API jménem přihlášeného uživatele. Aby to bylo možné, musí požádat o *delegovaná oprávnění*. Podrobnosti najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová rozhraní API: Konfigurace kódu](scenario-web-app-call-api-app-configuration.md)
