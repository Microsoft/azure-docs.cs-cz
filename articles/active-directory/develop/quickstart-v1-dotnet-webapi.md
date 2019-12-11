---
title: Vytvoření webového rozhraní API služby Azure AD .NET pro ověřování & autorizaci | Microsoft Docs
description: Zjistěte, jak vytvořit webové rozhraní API .NET MVC, které se integruje s Azure AD pro ověřování a autorizaci.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
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
ms.openlocfilehash: abc4c05aab6efcfc55d5e4dc812ab41a3e3fc8aa
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963269"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Rychlý start: Vytvoření webového rozhraní API .NET MVC, které se integruje s Azure AD pro ověřování a autorizaci

[Platforma Microsoft Identity Platform](v2-overview.md) je vývoj platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání rozhraní API od Microsoftu, jako jsou Microsoft Graph nebo rozhraní API, která vytvořili vývojáři.

[Knihovna Microsoft Authentication Library (MSAL)](msal-overview.md) umožňuje vývojářům získat tokeny z koncového bodu Microsoft Identity Platform, aby mohli přistupovat k zabezpečeným webovým rozhraním API. Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s koncovým bodem Microsoft Identity Platform (v 2.0).

Pro nová webová rozhraní API doporučujeme použít Microsoft Identity Platform (v 2.0) a MSAL k získání tokenů a přístup k zabezpečeným webovým rozhraním API: [rychlý Start: Přidání přihlašování přes Microsoft k webové aplikaci v ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
