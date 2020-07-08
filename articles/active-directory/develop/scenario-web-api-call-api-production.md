---
title: Přesunutí webového rozhraní API volání webových rozhraní API do produkčního prostředí – Microsoft Identity Platform | Azure
description: Přečtěte si, jak přesunout webové rozhraní API, které volá webová rozhraní API do produkčního prostředí.
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
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537147"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webové rozhraní API, které volá webová rozhraní API: Přesun do produkčního prostředí

Po získání tokenu pro volání webových rozhraní API můžete aplikaci přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Další informace

Teď, když znáte základy volání webových rozhraní API z vlastního webového rozhraní API, může vás zajímat následující kurz, který popisuje kód, který se používá k vytvoření chráněného webového rozhraní API, které volá webová rozhraní API.

| Ukázka | Platforma | Description |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebApi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | Webové rozhraní API ASP.NET Core 2,2, Desktop (WPF) | Rozhraní Web API ASP.NET Core 2,2 volá Microsoft Graph, které voláte z aplikace WPF pomocí platformy Microsoft identity (v 2.0). |
