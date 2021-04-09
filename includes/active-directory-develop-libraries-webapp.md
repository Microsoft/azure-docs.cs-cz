---
ms.openlocfilehash: 124290321815d22cad74d235205401b9380ff395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578361"
---
| Jazyk/rozhraní | Projekt zapnut<br/>GitHubu                                                                                     | Balíček                                                                                                    | Úvod<br/>Začínáme                               | Přihlášení uživatelů                                            | Přístup k webovým rozhraním API                                                    | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
| ASP.NET Core         | [Zabezpečení ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna nemůže žádat přístupové tokeny pro chráněná webová rozhraní API.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     |[Rychlý start](../articles/active-directory/develop/quickstart-v2-java-webapp.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
| Node.js              | [Uzel MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal – uzel](https://www.npmjs.com/package/@azure/msal-node)                                                | [Rychlý start](../articles/active-directory/develop/quickstart-v2-nodejs-webapp-msal.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport – Azure-AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Rychlý start](../articles/active-directory/develop/quickstart-v2-nodejs-webapp.md)      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [Rychlý start](../articles/active-directory/develop/quickstart-v2-python-webapp.md)      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y]    | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/