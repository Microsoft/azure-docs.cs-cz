---
title: Přihlaste se uživatelům a zavolejte Microsoft Graph API z aplikace .NET Desktop (WPF) | Microsoft Docs
description: Naučte se vytvořit desktopovou aplikaci pro Windows .NET, která se integruje se službou Azure AD za účelem přihlášení a volá rozhraní API chráněná službou Azure AD pomocí OAuth 2,0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1629cae69116f71428ccb150afda0ba668146631
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920953"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Rychlý Start: přihlášení uživatelů a volání rozhraní Microsoft Graph API z aplikace .NET Desktop (WPF)

[Platforma Microsoft Identity Platform](v2-overview.md) je vývoj platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání rozhraní API od Microsoftu, jako jsou Microsoft Graph nebo rozhraní API, která vytvořili vývojáři.

[Knihovna Microsoft Authentication Library (MSAL)](msal-overview.md) umožňuje vývojářům získat tokeny z koncového bodu Microsoft Identity Platform, aby mohli přistupovat k zabezpečeným webovým rozhraním API. Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s koncovým bodem Microsoft Identity Platform (v 2.0).

U nových aplikací klasické pracovní plochy doporučujeme použít Microsoft Identity Platform (v 2.0) a MSAL k získání tokenů a přístupu k zabezpečeným webovým rozhraním API: [rychlý Start: získání tokenu a volání Microsoft Graph API z desktopové aplikace pro Windows](quickstart-v2-windows-desktop.md)
