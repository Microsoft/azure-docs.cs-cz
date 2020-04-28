---
title: Jak funguje souhlas aplikace
description: Přečtěte si další informace o tom, jak prostředí pro vyjádření souhlasu Azure AD funguje, abyste zjistili, jak ho můžete používat při vývoji aplikací v Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 86b592ecf5485654ab6860addbfde48cee4df077
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885544"
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
