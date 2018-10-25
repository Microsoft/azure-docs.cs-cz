---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8d0b0c99b743461da41a518feae9c781f90c8b63
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988414"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Volání rozhraní Microsoft Graph API z JavaScriptu jednostránkové aplikace (SPA)

Tato příručka ukazuje, jak můžete JavaScript jednostránková aplikace (SPA) přihlašovat osobní pracovní a školní účty, získat přístupový token a volání rozhraní Microsoft Graph API nebo jiné rozhraní API, které vyžadují přístupových tokenů z koncového bodu Azure Active Directory v2.0.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázkové aplikace vygenerované v této příručce

![Jak funguje ukázkové aplikace vygenerované v této příručce](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Ukázková aplikace, které vytvořil tento průvodce umožňuje JavaScript SPA k dotazování rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Azure Active Directory v2.0. Pro tento scénář až se uživatel přihlásí, přístupový token se požadovaná a přidá do požadavky HTTP přes autorizační hlavičky. Získání tokenu a obnovení jsou zpracovány pomocí Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library pro JavaScript ve verzi Preview|

> [!NOTE]
> *msal.js* cílí *koncového bodu Azure Active Directory v2.0* -umožňující osobní, školní a pracovní účty pro přihlášení a získávat tokeny. *Koncového bodu Azure Active Directory v2.0* má [určitá omezení](..\articles\active-directory\develop\active-directory-v2-limitations.md).
> Chcete-li pochopit rozdíly mezi v1.0 a v2.0 koncové body, přečtěte si [Průvodce porovnáním koncový bod](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
