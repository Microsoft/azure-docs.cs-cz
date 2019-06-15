---
title: Webové rozhraní API, že volání webových rozhraní API (Přesun do produkčního prostředí) – platforma identit Microsoft
description: Zjistěte, jak vytvořit webové rozhraní API, že směru server-klient volání webových rozhraní API (Přesun do produkčního prostředí).
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074752"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Webové rozhraní API, která volá webové rozhraní API – Přesun do produkčního prostředí

Jakmile tokenu pro volání webových rozhraní API, které jste získali, můžete přesunout vaši aplikaci do produkčního prostředí.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Víc se uč

Teď, když znáte základy toho, jak volat webové rozhraní API z vašeho vlastního webového rozhraní API, vás může zajímat, tento kurz, který popisuje kód, který se má použít k sestavení chráněné webové rozhraní API volá webové rozhraní API.

| Ukázka | Platforma | Popis |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 webového rozhraní API, plochy (WPF) | ASP.NET Core 2.2 webového rozhraní API volání Microsoft Graphu, samotný volat z aplikace WPF pomocí Microsoft identity platform (v2.0) |
