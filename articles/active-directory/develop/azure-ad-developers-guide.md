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
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591206"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory pro vývojáře

Azure Active Directory (Azure AD) je cloudová služba identit umožňující vývojářům sestavovat aplikace, které bezpečně přihlásí uživatele s pracovním nebo školním účtem Microsoft. Azure AD podporuje vývojáře vytvářející jak obchodní aplikace s jedním tenantem, tak i vývojáře, kteří se zajímají o vyvíjení aplikací s více tenanty. Kromě základního přihlašování umožňuje Azure AD volat nejen rozhraní API Microsoftu, jako je [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview), ale i vlastní rozhraní API vytvořená na platformě Azure AD. Tato dokumentace vám ukáže, jak do vlastní aplikace přidat podporu Azure AD s použitím standardních oborových protokolů, jako jsou OAuth 2.0 a OpenID Connect.

> [!NOTE]
> Většina obsahu na této stránce se zaměřuje na koncový bod Azure AD v1.0, který podporuje pouze pracovní nebo školní účty Microsoft. Pokud chcete k přihlášení používat uživatelské nebo soukromé účty Microsoft, prostudujte si informace v tématu o [koncovému bodu Azure AD v2.0](active-directory-appmodel-v2-overview.md). Koncový bod Azure AD v2.0 nabízí jednotné vývojářské prostředí pro aplikace, které chtějí umožňovat jak přihlášení pomocí účtů Azure AD (pracovní a školní), tak i pomocí soukromých účtů Microsoft.

| | |
| --- | --- |
|[Základy ověřování](authentication-scenarios.md) | Úvod k ověřování pomocí Azure AD |
|[Typy aplikací](authentication-scenarios.md#application-types-and-scenarios) | Přehled scénářů ověřování, které podporuje Azure AD |      
| | |

## <a name="get-started"></a>Začínáme
Následující praktická nastavení vás provedou vytvořením aplikace na preferované platformě pomocí sady SDK Azure AD Authentication Library (ADAL). Pokud hledáte informace o použití knihovny Microsoft Authentication Library (MSAL), najdete je v dokumentaci o [koncovému bodu Azure AD v2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobilní a desktopové aplikace](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />Mobilní a desktopové aplikace</center> | [Přehled](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Webové aplikace](./media/azure-ad-developers-guide/Web_app.png)<br />Webové aplikace</center> | [Přehled](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![Jednostránkové aplikace](./media/azure-ad-developers-guide/SPA.png)<br />Jednostránkové aplikace</center> | [Přehled](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![Webová rozhraní API](./media/azure-ad-developers-guide/Web_API.png)<br />Webová rozhraní API</center> | [Přehled](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.js](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![Služba-služba](./media/azure-ad-developers-guide/Service_App.png)<br />Služba-služba</center> | [Přehled](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Návody
Tyto příručky vás provedou nejběžnějšími úkoly v Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registrace aplikace](quickstart-v1-integrate-apps-with-azure-ad.md)           | Postup registrace aplikace v Azure AD |
|[Víceklientské aplikace](howto-convert-app-to-be-multi-tenant.md)    | Postup přihlášení libovolného pracovního účtu Microsoft |
|[Protokoly OAuth a OpenID Connect](v1-protocols-openid-connect-code.md)| Postup přihlášení uživatelů a volání webových rozhraní API pomocí ověřovacích protokolů Microsoftu |
|  |  |

## <a name="reference-topics"></a>Referenční témata
Následující články poskytují podrobné informace o rozhraních API, zprávách protokolů a termínech používaných v Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Knihovny ověřování (ADAL)](active-directory-authentication-libraries.md)   | Přehled knihoven a sad SDK, které poskytuje Azure AD |
| [Ukázky kódu](sample-v1-code.md)                                  | Seznam všech ukázek kódu Azure AD |
| [Glosář](developer-glossary.md)                                      | Terminologie a definice slov, která se používají v této dokumentaci |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
