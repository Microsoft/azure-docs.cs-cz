---
title: Ověřování a autorizace
description: Seznamte se s různými způsoby, jak se aplikace nebo služba může ověřit pro prostorové kotvy Azure, a úrovně řízení, které musíte mít v bráně přístup k prostorovým kotvám Azure.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 133b565bc54feaf49a2fec9dd0056ca8e7ef43f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857720"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Ověřování a autorizace pro prostorové kotvy Azure

V této části se dozvíte o různých způsobech ověření pro prostorové kotvy Azure z vaší aplikace nebo webové služby a způsobů, jak můžete použít Role-Based Access Control v adresáři Azure (Azure AD) k řízení přístupu k vašim účtům prostorových kotev.

## <a name="overview"></a>Přehled

![Diagram, který zobrazuje přehled ověřování pro prostorové kotvy Azure.](./media/spatial-anchors-authentication-overview.png)

Aby klienti mohli získat přístup k danému účtu prostorových kotev Azure, musí nejdřív získat přístupový token ze služby tokenů zabezpečení (STS) ve službě Azure Mixed reality. Tokeny získané z služby STS Live po dobu 24 hodin a obsahují informace o službách prostorových ukotvení k tomu, aby mohl účet provádět autorizační rozhodnutí, a zajišťují, že k tomuto účtu budou mít přístup jenom autorizovaní objekty zabezpečení.

Přístupové tokeny se dají získat v Exchangi buď z klíčů účtu, nebo z tokenů vydaných službou Azure AD.

Klíče účtu umožňují rychle začít s používáním služby prostorových kotev Azure; před nasazením aplikace do produkčního prostředí však doporučujeme, abyste aplikaci aktualizovali tak, aby používala ověřování založené na službě Azure AD.

Ověřovací tokeny Azure AD lze získat dvěma způsoby:

- Pokud vytváříte podnikovou aplikaci a vaše společnost používá jako svůj systém identit službu Azure AD, můžete v aplikaci použít ověřování Azure AD založené na uživatelích a udělit přístup k účtům prostorových kotev pomocí stávajících skupin zabezpečení Azure AD nebo přímo uživatelům ve vaší organizaci.
- V opačném případě se doporučuje získat tokeny Azure AD z webové služby, která podporuje vaši aplikaci. Použití podpůrné webové služby je doporučovanou metodou ověřování pro produkční aplikace, protože se vyhnete vložení přihlašovacích údajů pro přístup k prostorovým kotvám Azure v klientské aplikaci.

## <a name="account-keys"></a>Klíče účtu

Nejjednodušší způsob, jak začít, je použití klíčů účtu pro přístup k účtu prostorových kotev Azure. Klíče účtu najdete na Azure Portal. Přejděte ke svému účtu a vyberte kartu klíče.

![Snímek obrazovky se zvýrazněnou stránkou s klíči s tlačítkem kopírovat u "primárního klíče".](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

K dispozici jsou dva klíče, které jsou současně platné pro přístup k účtu prostorových kotev. Doporučuje se pravidelně aktualizovat klíč, který používáte pro přístup k účtu. Existují dva samostatné platné klíče, které tyto aktualizace umožňují bez výpadků; musíte aktualizovat jenom primární klíč a sekundární klíč.

Sada SDK obsahuje integrovanou podporu pro ověřování pomocí klíčů účtu. jednoduše stačí nastavit vlastnost AccountKey objektu cloudSession.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[NDK C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Až to bude hotové, sada SDK bude pokládat s výměnou klíče účtu pro přístupový token a je nezbytné ukládat tokeny do mezipaměti pro vaši aplikaci.

> [!WARNING]
> Použití klíčů účtu se doporučuje pro rychlé zprovoznění, ale během vývoje a vytváření prototypů. Důrazně doporučujeme, abyste aplikaci nedodali do produkčního prostředí pomocí vloženého klíče účtu a místo toho používali přístup k ověřování Azure AD založeného na uživateli nebo službě na základě služby, které jsou uvedené dál.

## <a name="azure-ad-user-authentication"></a>Ověřování uživatelů Azure AD

U aplikací, které cílí na Azure Active Directory uživatele, se doporučuje použít pro uživatele token Azure AD, který můžete získat pomocí [knihovny MSAL](../../active-directory/develop/msal-overview.md). Měli byste postupovat podle kroků uvedených v [rychlém startu pro registraci aplikace](../../active-directory/develop/quickstart-register-app.md), mezi které patří:

1. Konfigurace v Azure Portal
    1.    Zaregistrujte svoji aplikaci ve službě Azure AD jako **nativní aplikaci**. V rámci registrace budete muset určit, jestli má aplikace více tenantů, nebo ne, a zadat adresy URL pro přesměrování povolené pro vaši aplikaci.
        1.  Přepnout na kartu **oprávnění rozhraní API**
        2.  Vyberte **Přidat oprávnění** .
            1.  Vybrat **poskytovatele prostředků hybridní reality** v **rozhraní API moje organizace používá** kartu
            2.  Vyberte **delegovaná oprávnění** .
            3.  Zaškrtněte políčko **mixedreality.** Přihlaste se pod **mixedreality**
            4.  Vyberte **Přidat oprávnění** .
        3.  Vyberte **udělit souhlas správce** .
    2.    Udělit vaší aplikaci nebo uživatelům přístup k vašemu prostředku:
        1.    Přejděte ke zdroji prostorových kotev v Azure Portal
        2.    Přepnout na kartu **řízení přístupu (IAM)**
        3.    Kliknutí na **Přidat přiřazení role**
            1.    [Vybrat roli](#role-based-access-control)
            2.    Do pole **Vybrat** zadejte jména uživatelů, skupin nebo aplikací, ke kterým chcete přiřadit přístup, a jejich skupiny (y).
            3.    Klikněte na **Uložit**.
2. V kódu:
    1.    Nezapomeňte použít **ID aplikace** a **identifikátor URI přesměrování** vlastní aplikace Azure AD jako **ID klienta** a parametry **RedirectUri** v MSAL.
    2.    Nastavte informace o tenantovi:
        1.    Pokud vaše aplikace podporuje **pouze moji organizaci**, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
        2.    Pokud vaše aplikace podporuje **účty v jakémkoli organizačním adresáři**, nahraďte tuto hodnotu **organizacemi** .
        3.    Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele**, nahraďte tuto hodnotu **běžnými** .
    3.    U žádosti o token nastavte **Rozsah** na https://sts.mixedreality.azure.com//.default . Tento obor oznamuje službě Azure AD, že vaše aplikace požaduje token pro službu tokenu zabezpečení (STS) pro Mixed reality.

V takovém případě by vaše aplikace měla být schopná získat z MSAL tokenu Azure AD; Tento token Azure AD můžete nastavit jako **authenticationToken** v objektu konfigurace cloudové relace.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[NDK C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Ověřování služby Azure AD

Doporučená možnost nasazení aplikací využívajících prostorové kotvy Azure do produkčního prostředí je využití služby back-end, která bude zprostředkovatelem žádosti o ověření. Obecné schéma by mělo být popsané v tomto diagramu:

![Přehled ověřování pro prostorové kotvy Azure](./media/spatial-anchors-aad-authentication.png)

V tomto případě se předpokládá, že vaše aplikace používá vlastní mechanismus (například účet Microsoft, PlayFab, Facebook, Google ID, vlastní uživatelské jméno a heslo atd.) a ověří se i pro její back-end službu. Po ověření vašich uživatelů pro back-end službu může tato služba získat token Azure AD, vyměnit ho pro přístupový token pro prostorové kotvy Azure a vrátit ho zpátky do klientské aplikace.

Přístupový token Azure AD se načte pomocí [knihovny MSAL](../../active-directory/develop/msal-overview.md). Měli byste postupovat podle kroků uvedených v [rychlém startu pro registraci aplikace](../../active-directory/develop/quickstart-register-app.md), mezi které patří:

1.    Konfigurace v Azure Portal:
    1.    Registrace aplikace ve službě Azure AD:
        1.    V Azure Portal přejděte na **Azure Active Directory**a vyberte **Registrace aplikací** .
        2.    Vybrat **registraci nové aplikace**
        3.    Zadejte název vaší aplikace, jako typ aplikace vyberte **Webová aplikace nebo rozhraní API** a zadejte adresu URL ověřování pro vaši službu. Pak stiskněte **vytvořit**.
        4.    V této aplikaci klikněte na **Nastavení**a pak vyberte kartu **certifikáty a tajné klíče** . Vytvořte nový tajný klíč klienta, vyberte dobu trvání a pak stiskněte **Přidat**. Nezapomeňte uložit tajnou hodnotu, protože ji budete muset zahrnout do kódu vaší webové služby.
    2.    Udělte vaší aplikaci nebo uživatelům přístup k vašemu prostředku:
        1.    Přejděte ke zdroji prostorových kotev v Azure Portal
        2.    Přepnout na kartu **řízení přístupu (IAM)**
        3.    Kliknutí na **Přidat přiřazení role**
        1.    [Vybrat roli](#role-based-access-control)
        2.    Do pole **Vybrat** zadejte název aplikace, kterou jste vytvořili a ke kterému chcete přiřadit přístup. Pokud chcete, aby uživatelé vaší aplikace měli různé role proti účtu prostorové kotvy, měli byste v Azure AD zaregistrovat několik aplikací a přiřadit je ke každé samostatné roli. Potom implementujte logiku autorizace pro použití správné role pro vaše uživatele.
        3.    Poznámka: v seznamu **Přidat přiřazení role** , ke kterému chcete **přiřadit přístup** , nastavte možnost uživatel, skupina nebo INSTANČNÍ objekt služby Azure AD.
    3.    Klikněte na **Uložit**.
2.    V kódu (Poznámka: můžete použít ukázku služby, která je součástí GitHubu):
    1.    Nezapomeňte použít ID aplikace, tajný klíč aplikace a identifikátor URI pro přesměrování vlastní aplikace Azure AD jako ID klienta, tajný klíč a parametry RedirectUri v MSAL.
    2.    Nastavte ID tenanta na vlastní ID tenanta Azure ADD v parametru autorita v MSAL.
    3.    U žádosti o token nastavte **Rozsah** na "". https://sts.mixedreality.azure.com//.default

V takovém případě může back-end služba získat token Azure AD. Pak ho může vyměňovat pro token MR, který se vrátí zpátky klientovi. Použití tokenu Azure AD k načtení tokenu MR se provádí prostřednictvím volání REST. Toto je ukázkové volání:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Záhlaví autorizace je formátováno takto: `Bearer <Azure_AD_token>`

A odpověď obsahuje token MR v prostém textu.

Tento token MR se pak vrátí klientovi. Klientská aplikace se pak může v konfiguraci cloudové relace nastavit jako přístupový token.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[NDK C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Aby bylo možné řídit úroveň přístupu udělených aplikacím, službám nebo uživatelům Azure AD vaší služby, byly vytvořeny následující role, které můžete podle potřeby přiřadit k účtům prostorových kotev Azure:

- **Vlastník účtu prostorových kotev**: aplikace nebo uživatelé, kteří mají tuto roli, mohou vytvářet prostorové kotvy, dotazovat se na ně a odstraňovat je. Při ověřování pro váš účet pomocí klíčů účtu se k ověřenému objektu zabezpečení přiřadí role **vlastníka účtu prostorových ukotvení** .
- **Přispěvatel účtu prostorových kotev**: aplikace nebo uživatelé, kteří mají tuto roli, mohou vytvářet prostorové kotvy, dotazovat se na ně, ale nemohou je odstranit.
- **Čtečka účtu prostorových kotev**: aplikace nebo uživatelé, kteří mají tuto roli, se můžou dotazovat jenom na prostorové kotvy, ale nemůžou vytvářet nové, odstraňovat existující nebo aktualizovat metadata na prostorových ukotveních. Tato možnost se obvykle používá pro aplikace, u kterých někteří uživatelé nastavili prostředí, zatímco ostatní mohou volat pouze kotvy dříve umístěné v daném prostředí.

## <a name="next-steps"></a>Další kroky

Vytvořte svou první aplikaci pomocí prostorových kotev Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
