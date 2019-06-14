---
title: Démon procesu aplikace volání webových rozhraní API (volání webových rozhraní API) - platforma identit Microsoft
description: Zjistěte, jak vytvořit aplikaci pro démona, volá webové rozhraní API (volání webových rozhraní API)
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076267"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Démon procesu aplikace, že volání webového rozhraní API – volání webového rozhraní API z aplikace

Démon aplikace můžete volat webové rozhraní API z aplikace .NET proces démon nebo volání několik předběžně schválené webového rozhraní API.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Volání webového rozhraní API z démona aplikace .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>Volání několik rozhraní API

Aplikace démonů webová rozhraní API, kterou je možné volat muset předem schválené. Nebudou existovat žádné přírůstkové souhlas s aplikacemi démon (neexistuje žádná interakce s uživatelem). Správce tenanta je potřeba předem souhlas aplikaci a všechna oprávnění rozhraní API. Pokud chcete volat několik rozhraní API, budete potřebovat k získání tokenu pro každý prostředek, každý čas volání `AcquireTokenForClient`. Knihovna MSAL pomocí mezipaměť tokenu aplikace Vyhněte se volání zbytečných služby.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Démon procesu aplikace – Přesun do produkčního prostředí](./scenario-daemon-production.md)