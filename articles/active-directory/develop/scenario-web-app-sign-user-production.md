---
title: Webová aplikace, které přihlásí uživatelé (Přesun do produkčního prostředí) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, který se přihlásí uživatelé (Přesun do produkčního prostředí)
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074707"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Webovou aplikaci, která se přihlásí uživatelé – Přesun do produkčního prostředí

Teď, když víte, jak k získání tokenu pro volání webových rozhraní API, zjistěte, jak ji přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další postup

### <a name="calling-web-apis-scenario"></a>Volání webového rozhraní API pro scénář

Jakmile web uživatelé vaší aplikace přihlásí, může volat webové rozhraní API jménem přihlášeného uživatele. Volání webového rozhraní API z webové aplikace je objekt následující scénáře:

> [!div class="nextstepaction"]
> [Webové aplikace, že volání webových rozhraní API](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Podrobné informace – kurz vývoje webové aplikace

Další informace o dalších způsobech uživatelé přihlásit s ASP.NET Core kurz: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Toto je progresivní kurz s produkčním připravený kód pro webové aplikace, včetně postupu pro přidání přihlášení.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
