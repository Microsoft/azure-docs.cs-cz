---
title: Azure Active Directory pro vývojáře | Dokumentace Microsoftu
description: Tento článek obsahuje přehled přihlašování pracovních a školních účtů Microsoft pomocí Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 77625cacd5d1497e6c18747cd8439a5fc76bdfb9
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399766"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory pro vývojáře

Azure Active Directory (Azure AD) je cloudová služba identit umožňující vývojářům sestavovat aplikace, které bezpečně přihlásí uživatele s pracovním nebo školním účtem Microsoft. Azure AD podporuje vývojáře vytvářející jak obchodní aplikace s jedním tenantem, tak i vývojáře, kteří se zajímají o vyvíjení aplikací s více tenanty. Kromě základního přihlašování umožňuje Azure AD volat nejen rozhraní API Microsoftu, jako je [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview), ale i vlastní rozhraní API vytvořená na platformě Azure AD. Tato dokumentace vám ukáže, jak do vlastní aplikace přidat podporu Azure AD s použitím standardních oborových protokolů, jako jsou OAuth 2.0 a OpenID Connect.

> [!NOTE]
> Většina obsahu na této stránce se zaměřuje na koncový bod Azure AD v1.0, který podporuje pouze pracovní nebo školní účty Microsoft. Pokud chcete k přihlášení používat uživatelské nebo soukromé účty Microsoft, prostudujte si informace v tématu o [koncovému bodu Azure AD v2.0](active-directory-appmodel-v2-overview.md). Koncový bod Azure AD v2.0 nabízí jednotné vývojářské prostředí pro aplikace, které chtějí umožňovat jak přihlášení pomocí účtů Azure AD (pracovní a školní), tak i pomocí soukromých účtů Microsoft.

| | |
| --- | --- |
|[Základy ověřování](active-directory-authentication-scenarios.md) | Úvod k ověřování pomocí Azure AD |
|[Typy aplikací](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Přehled scénářů ověřování, které podporuje Azure AD |      
| | |

## <a name="get-started"></a>Začínáme
Následující praktická nastavení vás provedou vytvořením aplikace na preferované platformě pomocí sady SDK Azure AD Authentication Library (ADAL). Pokud hledáte informace o použití knihovny Microsoft Authentication Library (MSAL), najdete je v dokumentaci o [koncovému bodu Azure AD v2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobilní a desktopové aplikace](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobilní a desktopové aplikace</center> | [Přehled](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Webové aplikace](./media/active-directory-developers-guide/Web_app.png)<br />Webové aplikace</center> | [Přehled](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) | |
| <center>![Jednostránkové aplikace](./media/active-directory-developers-guide/SPA.png)<br />Jednostránkové aplikace</center> | [Přehled](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Webová rozhraní API](./media/active-directory-developers-guide/Web_API.png)<br />Webová rozhraní API</center> | [Přehled](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Služba-služba](./media/active-directory-developers-guide/Service_App.png)<br />Služba-služba</center> | [Přehled](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Návody
Tyto příručky vás provedou nejběžnějšími úkoly v Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registrace aplikace](active-directory-integrating-applications.md)           | Postup registrace aplikace v Azure AD |
|[Víceklientské aplikace](active-directory-devhowto-multi-tenant-overview.md)    | Postup přihlášení libovolného pracovního účtu Microsoft |
|[Protokoly OAuth a OpenID Connect](active-directory-protocols-openid-connect-code.md)| Postup přihlášení uživatelů a volání webových rozhraní API pomocí ověřovacích protokolů Microsoftu |
|  |  |

## <a name="reference-topics"></a>Referenční témata
Následující články poskytují podrobné informace o rozhraních API, zprávách protokolů a termínech používaných v Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Knihovny ověřování (ADAL)](active-directory-authentication-libraries.md)   | Přehled knihoven a sad SDK, které poskytuje Azure AD |
| [Ukázky kódu](active-directory-code-samples.md)                                  | Seznam všech ukázek kódu Azure AD |
| [Glosář](active-directory-dev-glossary.md)                                      | Terminologie a definice slov, která se používají v této dokumentaci |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
