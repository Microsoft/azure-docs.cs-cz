---
title: Přesunutí webového rozhraní API volání webových rozhraní API do produkčního prostředí | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675868"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webové rozhraní API, které volá webová rozhraní API: Přesun do produkčního prostředí

Po získání tokenu pro volání webových rozhraní API je zde několik věcí, které je potřeba vzít v úvahu při přesunu aplikace do produkčního prostředí.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

Teď, když znáte základy volání webových rozhraní API z vlastního webového rozhraní API, může vás zajímat následující kurz, který popisuje kód, který se používá k vytvoření chráněného webového rozhraní API, které volá webová rozhraní API.

| Ukázka | Platforma | Description |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebApi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) kapitola 1 | ASP.NET Core webové rozhraní API, Desktop (WPF) | ASP.NET Core volání webového rozhraní API Microsoft Graph, které voláte z aplikace WPF pomocí platformy Microsoft identity. |
