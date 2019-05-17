---
title: Démon procesu aplikace volání webových rozhraní API (Přesun do produkčního prostředí) – platforma identit Microsoft
description: Zjistěte, jak vytvořit aplikaci pro proces démon, že volání webových rozhraní API (Přesun do produkčního prostředí)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545409"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Démon procesu aplikace, která volá webové rozhraní API – Přesun do produkčního prostředí

Teď, když víte, jak získat a pomocí tokenu pro volání služba služba, zjistěte, jak přesunout vaši aplikaci do produkčního prostředí.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Nasazení – případ aplikace démonů s více tenanty

Pokud jste nezávislý dodavatel softwaru vytváření démon aplikace, která poběží v několika tenantech, budete muset Ujistěte se, že správci tenanta:

- Zřídí instančního objektu pro aplikaci
- Uděluje souhlas pro aplikaci

Bude potřeba k vašim zákazníkům vysvětlují, jak tyto operace provést. Další informace najdete v tématu [požaduje souhlas pro celého tenanta](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další postup

Tady je pár odkazů na další informace:

### <a name="net"></a>.NET

- Pokud nemáte, vyzkoušejte si rychlý Start [získat token a volat Microsoft Graph API z konzoly aplikace pomocí identity aplikace](./quickstart-v2-netcore-daemon.md).
- Referenční dokumentace pro:
  - Vytvoření instance [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Volání [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Další ukázky a kurzy:
  - [Microsoft-identity-platform – konzoly – démon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) funkce jednoduchý proces démon konzolovou aplikaci .NET Core, který se zobrazí uživatelům tenanta dotazování Microsoft Graphu.

    ![topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Se stejným vzorkem také ukazuje na variantu s certifikáty.

    ![topologie](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-identity-Platform-ASPNET-WebApp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) funkce Webová aplikace ASP.NET MVC, která se synchronizuje data z Microsoft Graphu pomocí identity aplikace namísto toho jménem uživatele. Ukázka znázorňuje také procesu souhlas správce.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python je aktuálně ve verzi public preview. Další informace najdete v tématu [ukázka v úložiště přihlašovacích údajů klienta MSAL Pythonu](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

MSAL Python je aktuálně ve verzi public preview. Další informace najdete v tématu [MSAL Java v úložišti ukázek](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).