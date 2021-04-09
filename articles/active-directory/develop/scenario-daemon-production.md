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
ms.openlocfilehash: d903f04055d1607ee782bd502d99a8fd9cde87ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578443"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplikace démona, která volá webová rozhraní API – přesun do produkčního prostředí

Když teď víte, jak získat a použít token pro volání služby Service-to-Service, Naučte se, jak aplikaci přesunout do produkčního prostředí.

## <a name="deployment---multitenant-daemon-apps"></a>Nasazení – víceklientské aplikace démona

Pokud jste nezávislý výrobce softwaru, který vytváří aplikaci démona, která může běžet v několika klientech, ujistěte se, že správce klienta:

- Zřídí instanční objekt pro aplikaci.
- Udělí souhlas aplikaci.

Budete muset vysvětlit zákazníkům, jak tyto operace provést. Další informace najdete v tématu [vyžádání souhlasu pro celého tenanta](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Ukázky kódů

# <a name="net"></a>[.NET](#tab/dotnet)

- Referenční dokumentace pro:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)se vytváření instancí.
  - Volání [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Další ukázky/kurzy:
  - funkce [Microsoft-Identity-Platform-Console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) obsahuje malou konzolovou aplikaci .NET Core daemon, která zobrazuje uživatele Microsoft Graph dotazování tenanta.

    ![Ukázková topologie aplikace démona](media/scenario-daemon-app/daemon-app-sample.svg)

    Stejná ukázka také znázorňuje variaci s certifikáty:

    ![Ukázka topologie aplikace démona – certifikáty](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-WebApp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) obsahuje webovou aplikaci ASP.NET MVC, která synchronizuje data z Microsoft Graph pomocí identity aplikace, nikoli jménem uživatele. Tato ukázka také znázorňuje proces souhlasu správce.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

Vyzkoušejte si rychlý Start, [Získejte token a zavolejte Microsoft Graph API z konzolové aplikace Java pomocí identity aplikace](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Další informace naleznete v tématu:
  - Principy [Konfigurace](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)
  - Vytváření instancí [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)
  - [Nejčastější dotazy](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- Další ukázky/kurzy:
  - [Ukázka démona konzoly uzlu MSAL](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

Vyzkoušejte si rychlý Start, [Získejte token a zavolejte Microsoft Graph API z konzolové aplikace Pythonu pomocí identity aplikace](quickstart-v2-python-daemon.md).

---

## <a name="next-steps"></a>Další kroky

Tady je několik odkazů, které vám pomohou získat další informace:

# <a name="net"></a>[.NET](#tab/dotnet)

Vyzkoušejte si rychlý Start, [Získejte token a zavolejte Microsoft Graph API z konzolové aplikace .NET Core pomocí identity aplikace](quickstart-v2-netcore-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Vyzkoušejte si rychlý Start, [Získejte token a zavolejte Microsoft Graph API z konzolové aplikace Java pomocí identity aplikace](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Vyzkoušejte si rychlý Start, [Získejte token a zavolejte Microsoft Graph API z konzolové aplikace Node.js pomocí identity aplikace](quickstart-v2-nodejs-console.md).

# <a name="python"></a>[Python](#tab/python)

Vyzkoušejte si rychlý Start, [Získejte token a zavolejte Microsoft Graph API z konzolové aplikace Pythonu pomocí identity aplikace](quickstart-v2-python-daemon.md).

---
