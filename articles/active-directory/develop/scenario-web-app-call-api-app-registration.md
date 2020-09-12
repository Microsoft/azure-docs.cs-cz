---
title: Registrace webové aplikace, která volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Zjistěte, jak zaregistrovat webovou aplikaci, která volá webová rozhraní API.
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
ms.openlocfilehash: f94a3da96243e30faa90277ce86efec037f54672
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436458"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Webová aplikace, která volá webová rozhraní API: registrace aplikace

Webová aplikace, která volá webová rozhraní API, má stejnou registraci jako webová aplikace, která uživatele podepisuje. Postupujte proto podle pokynů ve [webové aplikaci, která se přihlásí uživatelům: registrace aplikace](scenario-web-app-sign-user-app-registration.md).

Vzhledem k tomu, že webová aplikace teď také volá webová rozhraní API, se z nich stala důvěrná klientská aplikace. To je důvod, proč je nutné provést další registraci. Aplikace musí sdílet přihlašovací údaje klienta nebo *tajné klíče*s platformou Microsoft identity.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění rozhraní API

Webové aplikace volají rozhraní API jménem přihlášeného uživatele. Aby to bylo možné, musí požádat o *delegovaná oprávnění*. Podrobnosti najdete v tématu [Přidání oprávnění pro přístup k webovému rozhraní API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová rozhraní API: Konfigurace kódu](scenario-web-app-call-api-app-configuration.md)
