---
title: Microsoft Identity Platform .NET Core démon | Microsoft Docs
description: Zjistěte, jak vytvořit aplikaci démona rozhraní .NET, která se integruje s Azure AD, volá rozhraní API chráněná službou Azure AD pomocí OAuth 2,0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f56f3f65919c4d1d9b59f7d0f67522a935579d64
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327182"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-an-apps-identity"></a>Rychlý start: Získání tokenu a volání Microsoft Graph API z konzolové aplikace pomocí identity aplikace

[Platforma Microsoft Identity Platform](v2-overview.md) je vývoj platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání rozhraní API od Microsoftu, jako jsou Microsoft Graph nebo rozhraní API, která vytvořili vývojáři.

[Knihovna Microsoft Authentication Library (MSAL)](msal-overview.md) umožňuje vývojářům získat tokeny z koncového bodu Microsoft Identity Platform, aby mohli přistupovat k zabezpečeným webovým rozhraním API. Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s koncovým bodem Microsoft Identity Platform (v 2.0).

Pro nové aplikace démona rozhraní .NET doporučujeme používat technologii Microsoft Identity Platform (v 2.0) a MSAL získat tokeny a přistupovat k zabezpečeným webovým rozhraním API: [Rychlé zprovoznění: Získejte token a zavolejte Microsoft Graph API z konzolové aplikace pomocí identity](quickstart-v2-netcore-daemon.md)aplikace.
