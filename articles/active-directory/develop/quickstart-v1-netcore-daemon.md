---
title: Získat token & volání rozhraní API Microsoft Graph z konzolových aplikací (v 1.0) | Azure
description: Vytvoření aplikace démona rozhraní .NET, která se integruje s Azure AD & volá rozhraní API chráněná službou Azure AD pomocí OAuth 2,0
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2858a0b14863195ee0f56c89fe2c55c9e63873de
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965921"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>Rychlý Start: získání tokenu a volání Microsoft Graph API pomocí identity konzolové aplikace (v 1.0) 

[Platforma Microsoft Identity Platform](v2-overview.md) je vývoj platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání rozhraní API od Microsoftu, jako jsou Microsoft Graph nebo rozhraní API, která vytvořili vývojáři.

[Knihovna Microsoft Authentication Library (MSAL)](msal-overview.md) umožňuje vývojářům získat tokeny z koncového bodu Microsoft Identity Platform, aby mohli přistupovat k zabezpečeným webovým rozhraním API. Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s koncovým bodem Microsoft Identity Platform (v 2.0).

## <a name="next-steps"></a>Další kroky

Pro nové aplikace démona rozhraní .NET doporučujeme používat technologii Microsoft Identity Platform (v 2.0) a MSAL k získání tokenů a přístup k zabezpečeným webovým rozhraním API: [rychlý Start: získání tokenu a volání Microsoft Graph API z konzolové aplikace pomocí identity aplikace](quickstart-v2-netcore-daemon.md).
