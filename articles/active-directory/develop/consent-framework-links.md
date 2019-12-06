---
title: Jak funguje souhlas s aplikacemi | Microsoft Docs
description: Přečtěte si další informace o tom, jak prostředí pro vyjádření souhlasu Azure AD funguje, abyste zjistili, jak ho můžete používat při vývoji aplikací v Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c79d9226b3a8965ad6ce853350f2e4c46617b5b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843423"
---
# <a name="how-application-consent-works"></a>Jak funguje souhlas aplikace

Tento článek vám umožní získat další informace o tom, jak architektura pro vyjádření souhlasu Azure AD funguje, abyste mohli efektivněji vyvíjet aplikace.

## <a name="recommended-documents"></a>Doporučené dokumenty

- Získejte Obecné informace o [tom, jak souhlas umožňuje vlastníkovi prostředku řídit přístup k prostředkům aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent).
- Získejte podrobný přehled o [tom, jak rozhraní pro vyjádření souhlasu Azure AD implementuje souhlas](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Podrobnější informace najdete v článku [o tom, jak aplikace pro více tenantů může používat souhlasu architektury](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) k implementaci souhlasu "User" a "admin", který podporuje pokročilejší vzory vícevrstvých aplikací.
- Podrobnější informace najdete [v tématu o tom, jak je podpora podporovaná na úrovni protokolu OAuth 2,0 během toku udělení autorizačního kódu.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>Další kroky
[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
