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
ms.openlocfilehash: 6871127ac138fb0113af73ca5222e3e2c2f99d7e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202535"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Volání Microsoft Graph API z jednoho jazyka JavaScript stránky aplikace (SPA)

Tato příručka ukazuje, jak můžete JavaScript aplikace pro jednu stránku (SPA) přihlásit osobní pracovní a školní účty, získání přístupového tokenu a volání rozhraní Graph API Microsoft nebo jiná rozhraní API, které vyžadují přístupové tokeny z koncového bodu v2 Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázková aplikace generované tímto průvodcem

![Jak funguje ukázková aplikace generované tímto průvodcem](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
## <a name="more-information"></a>Další informace

Ukázkové aplikace vytvořené v této příručce umožňuje SPA JavaScript do dotazu Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu v2 Azure Active Directory. Pro tento scénář, poté, co uživatel přihlásí je token přístupu požadovaná a přidat na požadavky HTTP přes autorizační hlavičky. Získání tokenu a obnovení, jsou zpracovávány pomocí knihovny Microsoft ověřování (MSAL).

<!--end-collapse-->

<!--start-collapse-->
## <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Knihovna Microsoft ověřování pro JavaScript Preview|

> [!NOTE]
> *msal.js* cíle *koncový bod služby Azure Active Directory v2* – což umožňuje osobní, školní a pracovní účty pro přihlášení a získávat tokeny. *Koncový bod služby Azure Active Directory v2* má [určitá omezení](..\articles\active-directory\develop\active-directory-v2-limitations.md). Pokud vás zajímá jenom pracovní a školní účty, použijte *adal.js* a *koncový bod V1*. Chcete-li pochopit rozdíly mezi v1 a v2 koncové body, přečtěte si [v1-v2 porovnání](..\articles\active-directory\develop\active-directory-v2-compare.md).

<!--end-collapse-->
