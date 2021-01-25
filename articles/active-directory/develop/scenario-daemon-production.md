---
title: Přesunutí aplikace démona, která volá webová rozhraní API do produkčního prostředí | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak přesunout aplikaci démona, která volá webová rozhraní API do produkčního prostředí.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 99fd79fb6c51f577d9b62d15ac006b068a685bcf
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756552"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplikace démona, která volá webová rozhraní API – přesun do produkčního prostředí

Když teď víte, jak získat a použít token pro volání služby Service-to-Service, Naučte se, jak aplikaci přesunout do produkčního prostředí.

## <a name="deployment---multitenant-daemon-apps"></a>Nasazení – víceklientské aplikace démona

Pokud jste nezávislý výrobce softwaru, který vytváří aplikaci démona, která může běžet v několika klientech, musíte se ujistit, že správce klienta:

- Zřídí instanční objekt pro aplikaci.
- Udělí souhlas aplikaci.

Budete muset vysvětlit zákazníkům, jak tyto operace provést. Další informace najdete v tématu [vyžádání souhlasu pro celého tenanta](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

Tady je několik odkazů, které vám pomohou získat další informace:

# <a name="net"></a>[.NET](#tab/dotnet)

- Rychlý Start: [získání tokenu a volání Microsoft Graph API z konzolové aplikace pomocí identity aplikace](./quickstart-v2-netcore-daemon.md)
- Referenční dokumentace pro:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)se vytváření instancí.
  - Volání [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Další ukázky/kurzy:
  - [Microsoft-Identity-Platform-Console-démon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) nabízí jednoduchou konzolovou aplikaci .NET Core daemon, která zobrazuje uživatele Microsoft Graph dotazování tenanta.

    ![Ukázková topologie aplikace démona](media/scenario-daemon-app/daemon-app-sample.svg)

    Stejná ukázka také znázorňuje variaci s certifikáty:

    ![Ukázka topologie aplikace démona – certifikáty](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-WebApp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) obsahuje webovou aplikaci ASP.NET MVC, která synchronizuje data z Microsoft Graph pomocí identity aplikace, nikoli jménem uživatele. Tato ukázka také znázorňuje proces souhlasu správce.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Vyzkoušejte si rychlý Start, [Získejte token a zavolejte Microsoft Graph API z konzolové aplikace Pythonu pomocí identity aplikace](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

MSAL Java je aktuálně ve verzi Public Preview. Další informace najdete v tématu [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---