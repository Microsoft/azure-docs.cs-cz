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
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518194"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webové rozhraní API, které volá webová rozhraní API: Přesun do produkčního prostředí

Po získání tokenu pro volání webových rozhraní API můžete aplikaci přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Další informace

Teď, když znáte základy volání webových rozhraní API z vlastního webového rozhraní API, může vás zajímat následující kurz, který popisuje kód, který se používá k vytvoření chráněného webového rozhraní API, které volá webová rozhraní API.

| Ukázka | Platforma | Description |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebApi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) kapitola 1 | ASP.NET Core webové rozhraní API, Desktop (WPF) | ASP.NET Core volání webového rozhraní API Microsoft Graph, které voláte z aplikace WPF pomocí technologie Microsoft Identity Platform (v 2.0). |
