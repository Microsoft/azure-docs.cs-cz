---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f70e0bcb68f059618f9b398a00e23498a10df23e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582977"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Volání rozhraní Microsoft Graph API z JavaScriptu jedním jednostránková aplikace (SPA)

Tato příručka ukazuje, jak můžete JavaScript jedné stránky aplikace (SPA) přihlašovat osobní pracovní a školní účty, získat přístupový token a volání rozhraní Microsoft Graph API nebo jiné rozhraní API, které vyžadují přístupových tokenů z koncového bodu Azure Active Directory v2.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázkové aplikace vygenerované v této příručce

![Jak funguje ukázkové aplikace vygenerované v této příručce](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Ukázková aplikace, které vytvořil tento průvodce umožňuje JavaScript SPA k dotazování rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Azure Active Directory v2. Pro tento scénář až se uživatel přihlásí, přístupový token je požadovaná a přidat na požadavky HTTP přes autorizační hlavičky. Získání tokenu a obnovení jsou zpracovány pomocí Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library pro JavaScript ve verzi Preview|

> [!NOTE]
> *msal.js* cílí *koncového bodu Azure Active Directory v2* -umožňující osobní, školní a pracovní účty pro přihlášení a získávat tokeny. *Koncového bodu Azure Active Directory v2* má [určitá omezení](..\articles\active-directory\develop\active-directory-v2-limitations.md). Pokud vás zajímá jenom v pracovní a školní účty, použijte *adal.js* a *koncového bodu V1*. Chcete-li pochopit rozdíly mezi v1 a v2 koncové body, přečtěte si [porovnání v1 a v2](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
