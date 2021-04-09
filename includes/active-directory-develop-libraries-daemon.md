---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007990"
---
| Jazyk/rozhraní | Projekt zapnut<br/>GitHubu                                                                 | Balíček                                                                                | Úvod<br/>Začínáme                           | Přihlášení uživatelů                                            | Přístup k webovým rozhraním API                                                 | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Rychlý start](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Uzel               | [Uzel MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal – uzel](https://www.npmjs.com/package/@azure/msal-node)  | [Rychlý start](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal – Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Rychlý start](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![Knihovna nemůže požádat o tokeny ID pro přihlášení uživatele.][n] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/