---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007884"
---
| Platforma          | Projekt zapnut<br/>GitHubu                                                                          | Balíček                                                                               | Úvod<br/>Začínáme                    | Přihlášení uživatelů                                         | Přístup k webovým rozhraním API                                                 | Všeobecně dostupná (GA) *nebo*<br/>Verze Public Preview<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Rychlý start](../articles/active-directory/develop/quickstart-v2-android.md) | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| iOS (SWIFT/obj-C) | [MSAL pro iOS a MacOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Kurz](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![Knihovna může vyžádat tokeny ID pro přihlášení uživatele.][y] | ![Knihovna může požadovat přístupové tokeny pro chráněná webová rozhraní API.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [dodatečné podmínky použití pro Microsoft Azure][preview-tos] Preview se vztahují na knihovny ve *verzi Public Preview*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/