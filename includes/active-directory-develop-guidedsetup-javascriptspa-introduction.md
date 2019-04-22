---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59502961"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Přihlašování uživatelů a volání rozhraní Microsoft Graph API z JavaScriptu jednostránkové aplikace (SPA)

Tato příručka ukazuje, jak JavaScript jednostránková aplikace (SPA) se můžete přihlásit osobní pracovní a školní účty, získat přístupový token a volání rozhraní Microsoft Graph API nebo jiné rozhraní API, které vyžadují přístupových tokenů z koncového bodu Microsoft identity platform.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázkové aplikace vygenerované v této příručce

![Ukazuje, jak ukázková aplikace vygenerované tohle funguje kurzy](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Ukázková aplikace, které vytvořil tento průvodce umožňuje JavaScript SPA k dotazování rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Microsoft identity platform. Pro tento scénář až se uživatel přihlásí, přístupový token se požadovaná a přidá do požadavky HTTP přes autorizační hlavičky. Získání tokenu a obnovení jsou zpracovány pomocí Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library pro JavaScript ve verzi Preview|

> [!NOTE]
> *msal.js* cíle *koncového bodu Microsoft identity platform* -umožňující osobní, školní a pracovní účty pro přihlášení a získávat tokeny. *Koncového bodu Microsoft identity platform* má [určitá omezení](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Chcete-li pochopit rozdíly mezi v1.0 a v2.0 koncové body, přečtěte si [Průvodce porovnáním koncový bod](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
