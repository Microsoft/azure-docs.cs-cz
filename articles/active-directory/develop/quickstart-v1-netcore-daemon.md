---
title: Získat token & volání Microsoft Graph (konzola .NET Core) (v 1.0) | Azure
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
ms.openlocfilehash: d44dfe3eb03ff086d3785311c34ab1a6a5b3982a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424056"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>Rychlý Start: získání tokenu a volání Microsoft Graph pomocí identity aplikace konzoly (v 1.0)

[Platforma Microsoft Identity Platform](v2-overview.md) je vývoj platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání rozhraní API od Microsoftu, jako jsou Microsoft Graph nebo rozhraní API, která vytvořili vývojáři.

[Knihovna Microsoft Authentication Library (MSAL)](msal-overview.md) umožňuje vývojářům získat tokeny z koncového bodu Microsoft Identity Platform, aby mohli přistupovat k zabezpečeným webovým rozhraním API. Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s koncovým bodem Microsoft Identity Platform (v 2.0).

## <a name="next-steps"></a>Další kroky

Pro nové aplikace démona rozhraní .NET doporučujeme používat technologii Microsoft Identity Platform (v 2.0) a MSAL k získání tokenů a přístup k zabezpečeným webovým rozhraním API: [rychlý Start: získání tokenu a volání Microsoft Graph API z konzolové aplikace pomocí identity aplikace](quickstart-v2-netcore-daemon.md).
