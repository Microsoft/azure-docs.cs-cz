---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007957"
---
| Jazyk/rozhraní | Projekt zapnut<br/>GitHub                                                                                     | Balíček                                                                               | Úvod<br/>Začínáme                        | Přihlášení uživatelů                                         | Přístup k webovým rozhraním API                                                 | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Chyt             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | Verze Public Preview                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| macOS (SWIFT/obj-C)  | [MSAL pro iOS a MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Kurz](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Kurz](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Kurz](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/