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
ms.openlocfilehash: 370bedf04dc61e2a637f735580cd4df14061264a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753332"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webové rozhraní API, které volá webová rozhraní API: Přesun do produkčního prostředí

Po získání tokenu pro volání webových rozhraní API můžete aplikaci přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Další informace

Teď, když znáte základy volání webových rozhraní API z vlastního webového rozhraní API, může vás zajímat následující kurz, který popisuje kód, který se používá k vytvoření chráněného webového rozhraní API, které volá webová rozhraní API.

| Ukázka | Platforma | Popis |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebApi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) kapitola 1 | ASP.NET Core webové rozhraní API, Desktop (WPF) | ASP.NET Core volání webového rozhraní API Microsoft Graph, které voláte z aplikace WPF pomocí platformy Microsoft identity. |
