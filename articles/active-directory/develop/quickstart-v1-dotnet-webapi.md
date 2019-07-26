---
title: Vytvoření webového rozhraní API .NET s Azure AD pro ověřování a autorizaci | Microsoft Docs
description: Zjistěte, jak vytvořit webové rozhraní API .NET MVC, které se integruje s Azure AD pro ověřování a autorizaci.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7891ea6aa2dbe10456c701b5e0216117f6ed27b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380710"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Rychlý start: Vytvoření webového rozhraní API .NET, které se integruje se službou Azure AD pro ověřování a autorizaci

[Platforma Microsoft Identity Platform](v2-overview.md) je vývoj platformy pro vývojáře Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání rozhraní API od Microsoftu, jako jsou Microsoft Graph nebo rozhraní API, která vytvořili vývojáři.

[Knihovna Microsoft Authentication Library (MSAL)](msal-overview.md) umožňuje vývojářům získat tokeny z koncového bodu Microsoft Identity Platform, aby mohli přistupovat k zabezpečeným webovým rozhraním API. Active Directory Authentication Library (ADAL) se integruje s koncovým bodem Azure AD for Developers (v 1.0), kde MSAL se integruje s koncovým bodem Microsoft Identity Platform (v 2.0).

Pro nová webová rozhraní API doporučujeme používat Microsoft Identity Platform (v 2.0) a MSAL k získání tokenů a přístup k zabezpečeným webovým rozhraním API: [Rychlé zprovoznění: Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
