---
title: Přesunutí webového rozhraní API volání webových rozhraní API do produkčního prostředí – Microsoft Identity Platform | Azure
description: Přečtěte si, jak přesunout webové rozhraní API, které volá webová rozhraní API do produkčního prostředí.
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
ms.openlocfilehash: 0d59a5b2a74c10e36103713725113cbe8c9cc412
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965165"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Webové rozhraní API, které volá webová rozhraní API – přesun do produkčního prostředí

Jakmile získáte token pro volání webových rozhraní API, můžete aplikaci přesunout do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Další informace

Teď, když znáte základy volání webových rozhraní API z vlastního webového rozhraní API, může vás zajímat tento kurz, který popisuje kód, který se používá k vytvoření chráněného webového rozhraní API, které volá webová rozhraní API.

| Ukázka | Platforma | Popis |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | Webové rozhraní API ASP.NET Core 2,2, Desktop (WPF) | Rozhraní Web API ASP.NET Core 2,2 volá Microsoft Graph, která se volá z aplikace WPF pomocí platformy Microsoft identity (v 2.0). |
