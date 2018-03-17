---
title: "Ukázky kódu Azure Active Directory | Microsoft Docs"
description: "Index ukázky kódu Azure Active Directory, uspořádané podle scénáře."
services: active-directory
documentationcenter: dev-center-name
author: msmbaldwin
manager: mtillman
editor: 
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: mbaldwin
ms.custom: aaddev
ms.openlocfilehash: 130d26828acd5394756c47f22217272338cc33e6
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="azure-active-directory-code-samples"></a>Ukázky kódu Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) můžete použít k přidání ověřování a autorizace do webových aplikací a webových rozhraní API. Tato část odkazy na ukázky, které ukazují, jak se provádí a fragmenty kódu, které můžete použít ve svých aplikacích. Na stránce ukázkový kód, najdete podrobné čtení-mi témata, které pomáhají s požadavky, instalace a nastavení. A kód je označeno jako komentář vám pomohou pochopit kritické oddíly.

Abyste pochopili základní scénáře pro každý typ ukázka, najdete v části scénáře ověřování pro Azure AD.

Podílet se na naše ukázky na Githubu: [Microsoft Azure Active Directory ukázky a dokumentace](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Webový prohlížeč, aby webové aplikace
Tyto ukázky ukazují, jak psát webové aplikace, která přesměruje prohlížeče uživatele pro jejich přihlášení k Azure AD.

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| C#/.NET |[WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Použijte OpenID Connect (ASP.Net OpenID Connect OWIN middleware) k ověřování uživatelů z klienta služby Azure AD. |
| C#/.NET |[WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Více klientů .NET MVC webové aplikace používající OpenID Connect (ASP.Net OpenID Connect OWIN middleware) k ověřování uživatelů od víc klientů Azure AD. |
| C#/.NET |[WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) |Použijte WS-Federation (WS-Federation OWIN ASP.Net middleware) k ověřování uživatelů z klienta služby Azure AD. |
| C# nebo .NET Core |[WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) |Webové aplikace .NET MVC, která používá OpenID Connect přihlášení uživatele z jednoho klienta Azure Active Directory (Azure AD) pomocí ASP.NET Core OpenID Connect middleware. |

## <a name="single-page-application-spa"></a>Jednostránkové aplikace (SPA)
Tento příklad ukazuje, jak psát jednostránkové aplikace zabezpečené s Azure AD.  

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| JavaScript, C#/.NET |[SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |K zabezpečení na základě AngularJS jednostránkové aplikace implementuje pomocí technologie ASP.NET web API back-end pomocí ADAL pro JavaScript a Azure AD. |

## <a name="native-application-to-web-api"></a>Nativní aplikace za účelem webového rozhraní API
Tyto ukázky kódu ukazují, jak vytvářet nativního klienta aplikace, které volají webové rozhraní API, které jsou zabezpečené službou Azure AD. Používají [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) a [OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| JavaScript |[NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) |Použijte k vytvoření aplikace Apache Cordova, která volá webové rozhraní API a používá Azure AD pro ověřování ADAL modul plug-in pro Apache Cordova. |
| C#/.NET |[NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) |Aplikace WPF v rozhraní .NET, která volá webové rozhraní API, která je zabezpečena pomocí Azure AD. |
| C#/.NET |[NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Aplikace Windows Store, která volá webové rozhraní API, která je zabezpečená službou Azure AD. |
| C#/.NET |[NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) |Aplikaci pro Windows Store volání víceklientské webového rozhraní API, která je zabezpečená službou Azure AD. |
| C#/.NET |[WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) |Nativní klientskou aplikaci, která volá webové rozhraní API, které získá token zastupovat původního uživatele a pak použije token volat jiné webové rozhraní API. |
| C#/.NET |[NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) |Aplikace Windows Store pro Windows Phone 8.1, která volá webové rozhraní API, která je zabezpečená službou Azure AD. |
| ObjC |[NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) |Aplikace pro iOS, která volá webové rozhraní API, která vyžaduje Azure AD pro ověřování. |
| C#/.NET |[WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) |Nativní klientskou aplikaci, která obsahuje logiku a zpracování token JWT v webového rozhraní API, místo použití OWIN middleware. |
| C#/Xamarin |[NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) |Xamarin vazbu k nativní Azure AD Authentication Library (ADAL) pro Android knihovny. |
| C#/Xamarin |[NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) |Xamarin vazbu k nativní Azure AD Authentication Library (ADAL) pro iOS. |
| C#/Xamarin |[NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) |Projekt Xamarin, který cílí pět platformy a volá webové rozhraní API, která je zabezpečená službou Azure AD. |
| C#/.NET |[NativeClient-Headless-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) |Nativní aplikace, který provede neinteraktivního ověřování a volá webové rozhraní API, která je zabezpečená službou Azure AD. |

## <a name="web-application-to-web-api"></a>Webovou aplikaci pro webové rozhraní API
Tyto ukázky kódu ukazují, jak používat [OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) k sestavení webových aplikací tohoto volání webového rozhraní API, které jsou zabezpečené službou Azure AD.

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| C#/.NET |[WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) |Volání webového rozhraní API s oprávněním přihlášeného uživatele. |
| C#/.NET |[WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) |Volání webového rozhraní API se oprávnění aplikace. |
| C#/.NET |[WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) |Přidat autorizační s [OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) do stávající webovou aplikaci, můžete ho volání webového rozhraní API. |
| JavaScript |[WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) |Nastavte službu REST API, která je integrovaná s Azure AD pro rozhraní API ochrany. Obsahuje server Node.js pomocí webového rozhraní API. |
| C#/.NET |[WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |Víceklientské MVC webové aplikace používající OpenID Connect (ASP.Net OpenID Connect OWIN middleware) k ověřování uživatelů z klienta služby Azure AD. Autorizační kód používá k volání rozhraní Graph API. |

## <a name="server-or-daemon-application-to-web-api"></a>Server nebo démon aplikaci pro webové rozhraní API
Tyto ukázky kódu ukazují, jak sestavit aplikaci démon nebo server, která se získá prostředky z webového rozhraní API pomocí [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) a [OAuth 2.0 ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| C#/.NET |[Daemon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) |Konzolové aplikace volá webové rozhraní API. Pověření klienta je heslo. |
| C#/.NET |[Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) |Konzolovou aplikaci, která volá webové rozhraní API. Pověření klienta je certifikát. |

## <a name="calling-microsoft-graph-api"></a>Volání metody Microsoft Graph API
Tyto ukázky kódu ukazují, jak vytvářet aplikace, které volají Microsoft Graph API ke čtení a zápis dat adresáře.

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| C#/.NET |[WebApp-MSGraphAPI-DotNet](https://github.com/microsoftgraph/aspnet-snippets-sample) |Webové aplikace, která používá rozhraní Microsoft Graph API pro přístup k datům adresáře Azure AD. |
| C#/.NET |[UWPApp-MSGraphAPI-DotNet](https://github.com/microsoftgraph/uwp-csharp-snippets-sample) |Tato aplikace pro univerzální platformu Windows se dozvíte, jak získat přístup k více prostředkům, včetně Microsoft Azure Active Directory (AD) a rozhraní API Office 365, tím, že požadavky na rozhraní Graph API Microsoft v aplikaci pro Windows 10. |

## <a name="calling-azure-ad-graph-api"></a>Volání rozhraní API Azure AD Graph
Tyto ukázky kódu ukazují, jak vytvářet aplikace, které volají Azure AD Graph API ke čtení a zápis dat adresáře.

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| Java |[WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) |Webové aplikace, která používá rozhraní Graph API pro přístup k datům adresáře Azure AD. |
| PHP |[WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |Webové aplikace, která používá rozhraní Graph API pro přístup k datům adresáře Azure AD. |
| C#/.NET |[ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) |Konzolovou aplikaci, která používá rozdílové dotazu v rozhraní Graph API k získání pravidelné změny uživatelské objekty v klient služby Azure AD. |
| C#/.NET |[WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) |Aplikace MVC používá rozhraní Graph API dotazy lze generovat organizační diagram jednoduché společnosti. |
| PHP |[WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) |Aplikace PHP, která volá rozhraní Graph API zaregistrovat rozšíření a potom číst, aktualizovat a odstraňovat hodnot v atributu rozšíření. |

## <a name="authorization"></a>Autorizace
Tyto ukázky kódu ukazují, jak používat Azure AD pro ověřování.

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| C#/.NET |[WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) |Proveďte řízení přístupu na základě role (RBAC) pomocí deklarace skupiny Azure Active Directory v aplikaci, která je integrovaná se službou Azure AD. |
| C#/.NET |[WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) |Proveďte řízení přístupu na základě role (RBAC) pomocí služby Azure Active Directory aplikační role v aplikaci, která je integrovaná se službou Azure AD. |

## <a name="legacy-walkthroughs"></a>Návody pro starší verze
Tyto postupy používají mírně starší technologii, ale stále může být důležité.

| Jazyk nebo platformu | Ukázka | Popis |
| --- | --- | --- |
| C#/.NET |[Ověřování na základě rolí a na základě seznamu ACL v aplikaci Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) |Proveďte ověření na základě role (RBAC) a autorizace na základě seznamu ACL v aplikaci, která je integrovaná se službou Azure AD. |
| C#/.NET |[AAL - aplikace pro Windows Store službě REST - ověřování](http://go.microsoft.com/fwlink/?LinkId=330605) |Použití [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) (dříve vrstev AAL) pro Windows Store Beta pro přidání možností ověřování uživatele do aplikace pro Windows Store. |
| C#/.NET |[ADAL – nativní aplikaci službě REST - ověřování pomocí AAD přes dialogové okno prohlížeče](http://go.microsoft.com/fwlink/?LinkId=259814) |Použití [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) pro přidání možností ověřování uživatele do klienta WPF. |
| C#/.NET |[Ověřování ADAL – nativní aplikaci službě REST - pomocí služby ACS přes dialogové okno prohlížeče](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |Použití [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) a [2.0 služby Řízení přístupu (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) pro přidání možností ověřování uživatele do klienta WPF. |
| C#/.NET |[ADAL – ověření serveru na Server](http://go.microsoft.com/fwlink/?LinkId=259816) |Použití [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) zabezpečit volání služby z procesu straně server do služby MVC4 webové rozhraní API REST. |
| C#/.NET |[Přidání přihlašování do webové aplikace pomocí služby Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Konfigurace aplikace .NET k provedení webové jednotné přihlašování s enterprise adresáře Azure AD. |
| C#/.NET |[Vývoj víceklientské webových aplikací s Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Na Azure AD můžete přidat na jednotné přihlašování a možnosti přístupu directory jeden aplikace .NET pro práci v několika organizacích. |
| JAVA |[Ukázková aplikace Java pro Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) |Pomocí rozhraní Graph API pro přístup k datům adresáře ze služby Azure AD. |
| PHP |[Ukázková aplikace PHP pro Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) |Pomocí rozhraní Graph API pro přístup k datům adresáře ze služby Azure AD. |
| C#/.NET |[Ukázková aplikace pro Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) |Pomocí rozhraní Graph API pro přístup k datům adresáře ze služby Azure AD. |
| C#/.NET |[Ukázková aplikace pro Azure AD Graph rozdílové dotazu](http://go.microsoft.com/fwlink/?LinkId=275398) |Použijte rozdílové dotazu v rozhraní Graph API k získání pravidelné změny uživatelské objekty v klient služby Azure AD. |
| C#/.NET |[Ukázková aplikace pro integraci víceklientské cloudových aplikací pro Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) |Integrované víceklientské aplikace do Azure AD. |
| C#/.NET |[Zabezpečení aplikaci pro Windows Store a webové služby REST pomocí služby Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Vytvoření jednoduché webové rozhraní API prostředky a klientské aplikace Windows Store pomocí služby Azure AD a [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md). |
| C#/.NET |[Pomocí Graph API k dotazování služby Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |Nakonfigurujte rozhraní Microsoft .NET aplikace pro používání Azure AD Graph API přístup k datům z klienta adresář služby Azure AD. |

## <a name="see-also"></a>Další informace najdete v tématech
##### <a name="other-resources"></a>Další prostředky
[Příručka pro vývojáře Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API koncepční a referenční informace](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API pomocné knihovny](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
