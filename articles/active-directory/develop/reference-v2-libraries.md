---
title: Microsoft Identity Platform Authentication Libraries | Azure
description: Seznam klientských knihoven a middlewaru kompatibilního s platformou Microsoft identity Pomocí těchto knihoven můžete přidat podporu přihlašování uživatelů (ověřování) a přístup k chráněnému webovému rozhraní API (autorizace) k vašim aplikacím.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218267"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Knihovny ověřování platformy Microsoft identity

V následujících tabulkách je uvedena podpora knihovny Microsoft Authentication Library pro několik typů aplikací. Obsahují odkazy na zdrojový kód knihovny, kde získat balíček pro projekt vaší aplikace a zda knihovna podporuje přihlášení uživatele (ověřování), přístup k chráněným webovým rozhraním API (autorizaci) nebo obojímu.

Microsoft Identity Platform byl certifikován OpenID Foundation jako [certifikovaný poskytovatel OpenID](https://openid.net/certification/). Pokud upřednostňujete použití jiné knihovny než knihovny Microsoft Authentication Library (MSAL) nebo jiné knihovny podporované společností Microsoft, vyberte jednu s [certifikací s certifikací OpenID Connect](https://openid.net/developers/certified/).

Pokud se rozhodnete ručně nastavit vlastní implementaci [OAuth 2,0 nebo OpenID 1,0 Connect](active-directory-v2-protocols.md)na úrovni protokolu, věnujte velkou pozornost faktorům zabezpečení v každé specifikaci Standard a použijte metodologii pro SDL (Software Development Lifecycle), jako je například [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Jednostránkové aplikace (SPA)

Jednostránkové aplikace běží zcela na ploše prohlížeče a načítá data stránek (HTML, CSS a JavaScript) dynamicky nebo v době načítání aplikace. Může volat webová rozhraní API pro interakci s back-end zdroji dat.

Vzhledem k tomu, že se kód SPA spouští zcela v prohlížeči, považuje se za *veřejného klienta* , který nedokáže bezpečně ukládat tajné klíče.

| Jazyk/rozhraní | Projekt zapnut<br/>GitHubu                                                                                                    | Balíček                                                                      | Úvod<br/>Začínáme                             | Přihlášení uživatelů                                         | Přístup k webovým rozhraním API                                                 | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL úhlová 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | Verze Public Preview                                               |
| Angular              | [MSALý úhlový](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Kurz](tutorial-v2-angular.md)              | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | Verze Public Preview                                               |
| JavaScript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Kurz](tutorial-v2-javascript-auth-code.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| React                | [MSAL reagovat](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | Verze Public Preview                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

## <a name="web-application"></a>Webová aplikace

Webová aplikace spouští kód na serveru, který generuje a odesílá HTML, CSS a JavaScript do webového prohlížeče uživatele, aby jej bylo možné vykreslit. Identita uživatele je udržována jako relace mezi prohlížečem uživatele (front-end) a webovým serverem (back-end).

Vzhledem k tomu, že kód webové aplikace běží na webovém serveru, je považován za *důvěrného klienta* , který dokáže bezpečně ukládat tajné klíče.

| Jazyk/rozhraní | Projekt zapnut<br/>GitHubu                                                                                     | Balíček                                                                                                    | Úvod<br/>Začínáme                               | Přihlášení uživatelů                                            | Přístup k webovým rozhraním API                                                    | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
| ASP.NET Core         | [Zabezpečení ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna nemůže žádat přístupové tokeny pro chráněná webová rozhraní API.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Rychlý start](quickstart-v2-java-webapp.md)        | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
| Node.js              | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal – uzel](https://www.npmjs.com/package/@azure/msal-node)                                                | [Rychlý start](quickstart-v2-nodejs-webapp-msal.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | Verze Public Preview                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport – Azure-AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Rychlý start](quickstart-v2-nodejs-webapp.md)      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna nemůže žádat přístupové tokeny pro chráněná webová rozhraní API.][n] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Rychlý start](quickstart-v2-python-webapp.md)      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

## <a name="desktop-application"></a>Desktopová aplikace

Aplikace klasické pracovní plochy je obvykle binární (zkompilovaný) kód, který je v uživatelském rozhraní, a je určen ke spuštění na ploše uživatele.

Vzhledem k tomu, že se desktopová aplikace spouští na ploše uživatele, považuje se za *veřejného klienta* , který nedokáže bezpečně ukládat tajné klíče.

| Jazyk/rozhraní | Projekt zapnut<br/>GitHubu                                                                                     | Balíček                                                                               | Úvod<br/>Začínáme                        | Přihlášení uživatelů                                         | Přístup k webovým rozhraním API                                                 | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Chyt             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | Verze Public Preview                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| macOS (SWIFT/obj-C)  | [MSAL pro iOS a MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Kurz](tutorial-v2-ios.md)             | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Kurz](tutorial-v2-windows-uwp.md)     | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Kurz](tutorial-v2-windows-desktop.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

## <a name="mobile-application"></a>Mobilní aplikace

Mobilní aplikace je obvykle binární (zkompilovaný) kód, který je na uživatelském rozhraní a je určen ke spuštění na mobilním zařízení uživatele.

Vzhledem k tomu, že mobilní aplikace běží na mobilním zařízení uživatele, je považována za *veřejného klienta* , který nedokáže bezpečně ukládat tajné klíče.

| Platforma          | Projekt zapnut<br/>GitHubu                                                                          | Balíček                                                                               | Úvod<br/>Začínáme                    | Přihlášení uživatelů                                         | Přístup k webovým rozhraním API                                                 | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Rychlý start](quickstart-v2-android.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| iOS (SWIFT/obj-C) | [MSAL pro iOS a MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Kurz](tutorial-v2-ios.md)         | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

## <a name="service--daemon"></a>Služba/démon

Služby a procesy démony se běžně používají pro komunikaci typu Server-Server a jinou bezobslužnou ( *někdy označovanou jako bezobslužné*). Vzhledem k tomu, že na klávesnici není žádný uživatel, aby bylo možné zadat přihlašovací údaje nebo souhlas s přístupem k prostředkům, tyto aplikace se při žádosti o autorizovaný přístup k prostředkům webového rozhraní API ověřují jako samy sebe, nikoli jako uživatel.

Služba nebo démon, který běží na serveru, se považuje za *důvěrného klienta* , který může své tajné údaje ukládat bezpečně.

| Jazyk/rozhraní | Projekt zapnut<br/>GitHubu                                                                 | Balíček                                                                                | Úvod<br/>Začínáme                           | Přihlášení uživatelů                                            | Přístup k webovým rozhraním API                                                 | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Rychlý start](quickstart-v2-netcore-daemon.md) | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal – Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Rychlý start](quickstart-v2-python-daemon.md)  | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

## <a name="next-steps"></a>Další kroky

Další informace o knihovně ověřování společnosti Microsoft najdete v tématu [Přehled knihovny Microsoft Authentication Library (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
