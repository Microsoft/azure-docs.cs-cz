---
title: Jak funguje souhlas pro aplikace
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
ms.openlocfilehash: 15270a998aff174c04acf2969d984eb022852635
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117358"
---
# <a name="how-application-consent-works"></a>Jak funguje souhlas pro aplikace

Tento článek vám umožní získat další informace o tom, jak architektura pro vyjádření souhlasu Azure AD funguje, abyste mohli efektivněji vyvíjet aplikace.

## <a name="recommended-documents"></a>Doporučené dokumenty

- Získejte Obecné informace o [tom, jak souhlas umožňuje vlastníkovi prostředku řídit přístup k prostředkům aplikace](./developer-glossary.md#consent).
- Získejte podrobný přehled o [tom, jak rozhraní pro vyjádření souhlasu Azure AD implementuje souhlas](./quickstart-register-app.md).
- Podrobnější informace najdete v článku [o tom, jak aplikace pro více tenantů může používat souhlasu architektury](./howto-convert-app-to-be-multi-tenant.md) k implementaci souhlasu "User" a "admin", který podporuje pokročilejší vzory vícevrstvých aplikací.
- Podrobnější informace najdete [v tématu o tom, jak je podpora podporovaná na úrovni protokolu OAuth 2,0 během toku udělení autorizačního kódu.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Další kroky
[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)