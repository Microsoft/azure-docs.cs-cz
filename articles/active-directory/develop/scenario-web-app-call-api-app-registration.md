---
title: Registrace webové aplikace, která volá webová rozhraní API – platforma microsoft identit | Azure
description: Přečtěte si, jak zaregistrovat webovou aplikaci, která volá webová api
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881872"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Webová aplikace, která volá webová api: Registrace aplikace

Webová aplikace, která volá webová api, má stejnou registraci jako webová aplikace, která přiřazuje uživatele. Postupujte podle pokynů ve [webové aplikaci, která se připisuje uživatelům: Registrace aplikace](scenario-web-app-sign-user-app-registration.md).

Protože však webová aplikace nyní také volá webová api, stane se důvěrnou klientskou aplikací. To je důvod, proč je nutná nějaká další registrace. Aplikace musí sdílet přihlašovací údaje klienta nebo *tajné klíče*s platformou identit microsoftu.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Oprávnění rozhraní API

Webové aplikace volají rozhraní API jménem přihlášeného uživatele. K tomu musí požádat o *delegovaná oprávnění*. Podrobnosti naleznete v tématu [Přidání oprávnění pro přístup k webovým sítím API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webová aplikace, která volá webová api: konfigurace kódu](scenario-web-app-call-api-app-configuration.md)
