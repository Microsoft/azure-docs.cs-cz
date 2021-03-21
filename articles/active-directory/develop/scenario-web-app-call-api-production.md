---
title: Přesun do produkční webové aplikace, která volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak přesunout do produkční webové aplikace, která volá webová rozhraní API.
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
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675936"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Webová aplikace, která volá webová rozhraní API: přesunout do produkčního prostředí

Teď, když víte, jak získat token pro volání webových rozhraní API, je zde několik věcí, které je potřeba vzít v úvahu při přesunu aplikace do produkčního prostředí.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si kompletní postupný kurz pro ASP.NET Core Web Apps. Tento kurz:

- Ukazuje, jak podepisovat uživatele do více cílových skupin nebo do národních cloudů nebo pomocí sociálních identit.
- Volá Microsoft Graph.
- Volá několik rozhraní API Microsoftu.
- Zpracovává přírůstkový souhlas.
- Volá vaše vlastní webové rozhraní API.

[Kurz ASP.NET Core webové aplikace](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
