---
title: Přesunutí aplikace pro daemon, která volá webová rozhraní API do produkčního prostředí – platforma identit Microsoftu | Azure
description: Přečtěte si, jak přesunout aplikaci pro daemon, která volá webová API do produkčního prostředí.
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
ms.openlocfilehash: e5d25169aa3daee33a9f02e605cbff91776dc7f1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885425"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon aplikace, která volá webOVÁ API - přejít na produkční

Teď, když víte, jak získat a použít token pro volání služby služby, přečtěte si, jak přesunout aplikaci do produkčního prostředí.

## <a name="deployment---multitenant-daemon-apps"></a>Nasazení – víceklientské aplikace pro daemony

Pokud jste isv vytvoření daemon aplikace, která může běžet v několika tenantů, musíte se ujistit, že správce klienta:

- Zřídí instanční objekt pro aplikaci.
- Uděluje souhlas s žádostí.

Budete muset vysvětlit svým zákazníkům, jak provádět tyto operace. Další informace najdete [v tématu Požadování souhlasu pro celého klienta](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

Zde je několik odkazů, které vám pomohou dozvědět se více:

# <a name="net"></a>[.NET](#tab/dotnet)

- Úvodní příručka: [Získejte token a zavolejte rozhraní Microsoft Graph API z konzolové aplikace pomocí identity aplikace](./quickstart-v2-netcore-daemon.md).
- Referenční dokumentace pro:
  - Vytvoření instance [aplikace ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Volání [acquiretokenforclient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Další ukázky/výukové programy:
  - [Microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) obsahuje jednoduchou konzolovou aplikaci .NET Core, která zobrazuje uživatele klienta dotazujícího se na microsoft graph.

    ![Ukázka topologie aplikace daemon](media/scenario-daemon-app/daemon-app-sample.svg)

    Stejný vzorek také ilustruje variantu s certifikáty:

    ![Ukázková topologie aplikací daemon - certifikáty](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) obsahuje ASP.NET webové aplikace MVC, která synchronizuje data z microsoft graphu pomocí identity aplikace namísto jménem uživatele. Tato ukázka také ilustruje proces souhlasu správce.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Zkuste rychlý start [Získat token a volat rozhraní Microsoft Graph API z konzolové aplikace Pythonu pomocí identity aplikace](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

MSAL Java je v současné době ve verzi Public Preview. Další informace naleznete v [tématu MSAL Java dev samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
