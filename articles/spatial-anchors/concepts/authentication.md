---
title: Ověřování a autorizace
description: Přečtěte si o různých způsobech, jak se aplikace nebo služba může ověřit na Azure Spatial Anchors, a o úrovních řízení, které máte k převozu přístupu k prostorovým kotvám Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656965"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Ověřování a autorizace pro prostorové kotvy Azure

V této části se budeme zabývat různými způsoby ověřování na Azure Prostorové kotvy z vaší aplikace nebo webové služby a způsoby, ve kterém můžete použít řízení přístupu na základě rolí v Azure Directory (Azure AD) k řízení přístupu k účtům prostorových ukotvení.

## <a name="overview"></a>Přehled

![Přehled ověřování pro azure prostorové kotvy](./media/spatial-anchors-authentication-overview.png)

Chcete-li získat přístup k danému účtu Azure Spatial Anchors, klienti musí nejprve získat přístupový token ze služby Token zabezpečení Azure Mixed Reality (STS). Tokeny získané z STS live po dobu 24 hodin a obsahují informace pro služby prostorové kotvy, aby se rozhodnutí o autorizaci na účet a zajistit, že pouze oprávněné objekty zabezpečení přístup k tomuto účtu.

Přístupové tokeny lze získat výměnou buď z klíče účtu nebo z tokenů vydaných Azure AD.

Klíče účtu umožňují rychle začít používat službu Azure Spatial Anchors; než však nasadíte aplikaci do produkčního prostředí, doporučujeme aktualizovat aplikaci tak, aby používala ověřování na základě Azure AD.

Ověřovací tokeny Azure AD lze získat dvěma způsoby:

- Pokud vytváříte podnikovou aplikaci a vaše společnost používá Azure AD jako svůj systém identit, můžete ve své aplikaci používat uživatelské ověřování Azure AD a udělit přístup k účtům prostorových ukotvení pomocí stávajících skupin zabezpečení Azure AD nebo uživatelům ve vaší organizaci.
- V opačném případě se doporučuje získat tokeny Azure AD z webové služby podporující vaši aplikaci. Použití podpůrné webové služby je doporučená metoda ověřování pro produkční aplikace, protože se vyhýbá vkládání přihlašovacích údajů pro přístup k prostorovým kotvicím Azure do klientské aplikace.

## <a name="account-keys"></a>Klíče účtu

Použití klíčů účtu pro přístup k účtu Azure Spatial Anchors je nejjednodušší způsob, jak začít. Klíče svého účtu najdete na webu Azure Portal. Přejděte ke svému účtu a vyberte kartu "Klíče".

![Přehled ověřování pro azure prostorové kotvy](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Jsou k dispozici dva klíče, které jsou současně platné pro přístup k účtu prostorové kotvy. Doporučujeme pravidelně aktualizovat klíč, který používáte pro přístup k účtu. mít dva samostatné platné klíče umožňují tyto aktualizace bez prostoje; stačí aktualizovat alternativně primární klíč a sekundární klíč.

Sada SDK má integrovanou podporu pro ověřování pomocí klíčů účtů. stačí nastavit AccountKey vlastnost na cloudSession objektu.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

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

Po dokončení bude sada SDK zpracovávat výměnu klíče účtu pro přístupový token a nezbytné ukládání tokenů do mezipaměti pro vaši aplikaci.

> [!WARNING]
> Použití klíčů účtu se doporučuje pro rychlé napalubě, ale pouze při vývoji / prototypování. Důrazně doporučujeme nedoručovat vaši aplikaci do produkčního prostředí pomocí vloženého klíče účtu a místo toho použít přístupy ověřování Azure AD založené na uživateli nebo službě uvedené dále.

## <a name="azure-ad-user-authentication"></a>Ověřování uživatelů Azure AD

Pro aplikace zaměřené na uživatele služby Azure Active Directory je doporučeným přístupem použití tokenu Azure AD pro uživatele, který můžete získat pomocí [knihovny MSAL](../../active-directory/develop/msal-overview.md). Postupujte podle kroků uvedených v [seznamu zaregistrovat aplikaci rychlý start](../../active-directory/develop/quickstart-register-app.md), mezi něž patří:

1. Konfigurace na webu Azure Portal
    1.  Zaregistrujte svou aplikaci ve službě Azure AD jako **nativní aplikaci**. Jako součást registrace budete muset určit, zda vaše aplikace by měla být víceklientské nebo ne, a poskytnout adresy URL přesměrování povolené pro vaši aplikaci.
        1.  Přepnutí na kartu **Oprávnění rozhraní API**
        2.  Vyberte **Přidat oprávnění.**
            1.  V části **API, která moje organizace používá, vyberte** **zprostředkovatele prostředků pro smíšenou realitu.**
            2.  Vybrat **delegovaná oprávnění**
            3.  Zaškrtněte políčko pro **mixedreality.signin** pod **mixedreality**
            4.  Vybrat **přidat oprávnění**
        3.  Vybrat **Možnost Udělit souhlas správce.**
    2.  Udělte aplikaci nebo uživatelům přístup k prostředku:
        1.  Přechod na prostředek prostorových ukotvení na webu Azure Portal
        2.  Přepnutí na kartu **Řízení přístupu (IAM)**
        3.  Přístup k **přidání přiřazení role**
            1.  [Vybrat roli](#role-based-access-control)
            2.  Do pole **Vybrat** zadejte jméno uživatele, skupin y a/nebo aplikací, ke kterým chcete přiřadit přístup.
            3.  Klikněte na **Uložit**.
2. Ve vašem kódu:
    1.  Ujistěte se, že používáte **ID aplikace** a **přesměrovat Uri** vlastní aplikace Azure AD jako **id klienta** a **redirectUri** parametry v ADAL
    2.  Nastavte informace o klientovi:
        1.  Pokud vaše aplikace podporuje **pouze moje organizace**, nahraďte tuto hodnotu **id klienta** nebo **názvem klienta** (například contoso.microsoft.com)
        2.  Pokud vaše aplikace podporuje **účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu **organizacemi**
        3.  Pokud vaše aplikace podporuje **všechny uživatele účtu Microsoft**, nahraďte tuto hodnotu **běžnými**
    3.  Na požadavek tokenu **resource** nastavtehttps://sts.mixedreality.azure.comprostředek na " ". Tento "prostředek" bude znamenat azure a d.P., že vaše aplikace požaduje token pro službu Azure Spatial Anchors.

S tím vaše aplikace by měla být schopna získat z MSAL token Azure AD; Můžete nastavit, že token Azure AD jako **authenticationToken** na vašem objektu konfigurace cloudové relace.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

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

Doporučená možnost nasazení aplikací využívajících Azure Spatial Anchors do produkčního prostředí je využít back-endovou službu, která zprostředkuje požadavky na ověřování. Obecný režim by měl být popsán v tomto diagramu:

![Přehled ověřování pro azure prostorové kotvy](./media/spatial-anchors-aad-authentication.png)

Zde se předpokládá, že vaše aplikace používá svůj vlastní mechanismus (například: účet Microsoft, PlayFab, Facebook, Google ID, vlastní uživatelské jméno / heslo atd.) k ověření na jeho back-endové služby. Jakmile vaši uživatelé jsou ověřeny do vaší back-endové služby, tato služba můžete načíst token Azure AD, vyměnit za přístupový token pro Azure Spatial Anchors a vrátit zpět do klientské aplikace.

Přístupový token Azure AD se načítá pomocí [knihovny MSAL](../../active-directory/develop/msal-overview.md). Postupujte podle kroků uvedených v [seznamu zaregistrovat aplikaci rychlý start](../../active-directory/develop/quickstart-register-app.md), mezi něž patří:

1.  Konfigurace na webu Azure Portal:
    1.  Zaregistrujte svou aplikaci ve službě Azure AD:
        1.  Na webu Azure Portal přejděte do **Služby Azure Active Directory**a vyberte **registrace aplikací.**
        2.  Vybrat **novou registraci aplikace**
        3.  Zadejte název aplikace, jako typ aplikace vyberte **Webovou aplikaci / ROZHRANÍ API** a zadejte adresu URL auth pro vaši službu. Pak stiskněte **klávesu Vytvořit**.
        4.  V této aplikaci klikněte na **Nastavení**a pak vyberte kartu **Klíče.** **Save** Nezapomeňte uložit hodnotu klíče, která se v té době zobrazí, protože ji budete muset zahrnout do kódu webové služby.
    2.  Udělte aplikaci nebo uživatelům přístup k prostředku:
        1.  Přechod na prostředek prostorových ukotvení na webu Azure Portal
        2.  Přepnutí na kartu **Řízení přístupu (IAM)**
        3.  Přístup k **přidání přiřazení role**
        1.  [Vybrat roli](#role-based-access-control)
        2.  Do **výběrového** pole zadejte název aplikace, kterou jste vytvořili, a ke které chcete přiřadit přístup. Pokud chcete, aby uživatelé vaší aplikace měli různé role oproti účtu Prostorové kotvy, měli byste zaregistrovat více aplikací ve službě Azure AD a přiřadit každé samostatné roli. Pak implementujte logiku autorizace a použijte správnou roli pro uživatele.
    3.  Klikněte na **Uložit**.
2.  Ve vašem kódu (poznámka: můžete použít ukázku služby zahrnutou na GitHubu):
    1.  Ujistěte se, že používáte ID aplikace, tajný klíč aplikace a přesměrovat Uri vlastní aplikace Azure AD jako parametry ID klienta, tajný klíč a RedirectUri v ADAL
    2.  Nastavení ID klienta na vlastní ID klienta AAAzure ADD v parametru autority v ADAL
    3.  Na požadavek tokenu **resource** nastavtehttps://sts.mixedreality.azure.comprostředek na " "

S tím vaše back-endová služba můžete načíst token Azure AD. Potom jej můžete vyměnit za token MR, který se vrátí zpět klientovi. Pomocí tokenu Azure AD k načtení tokenu MR se provádí prostřednictvím volání REST. Zde je ukázka hovoru:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Pokud je hlavička Autorizace formátována takto:`Bearer <accoundId>:<accountKey>`

A odpověď obsahuje token MR ve formátu prostého textu.

Tento mr token je pak vrácena klientovi. Vaše klientská aplikace ji pak může nastavit jako přístupový token v konfiguraci cloudové relace.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

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

Chcete-li pomoci řídit úroveň přístupu uděleného aplikacím, službám nebo uživatelům služby Azure AD vaší služby, byly vytvořeny následující role, které můžete podle potřeby přiřadit k vašim účtům Azure Spatial Anchors:

- **Vlastník účtu prostorových kotev**: aplikace nebo uživatelé, kteří mají tuto roli, jsou schopni vytvářet prostorové kotvy, dotazovat se na ně a odstraňovat je. Při ověřování na účet pomocí klíčů účtu, vlastník **účtu prostorové kotvy** je přiřazen a ověřený objekt zabezpečení.
- **Přispěvatel účtu prostorových ukotvení**: aplikace nebo uživatelé, kteří mají tuto roli, jsou schopni vytvářet prostorové kotvy, dotazovat se na ně, ale nemohou je odstranit.
- **Čtečka účtů prostorových ukotvení**: aplikace nebo uživatelé, kteří mají tuto roli, mohou dotazovat pouze na prostorové kotvy, ale nemohou vytvářet nové, odstraňovat existující nebo aktualizovat metadata na prostorových kotvách. To se obvykle používá pro aplikace, kde někteří uživatelé kaplan prostředí, zatímco jiní mohou vyvolat pouze kotvy dříve umístěné v tomto prostředí.

## <a name="next-steps"></a>Další kroky

Vytvořte si první aplikaci s Azure Spatial Anchors.

> [!div class="nextstepaction"]
> [Jednota (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Jednota (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Jednota (Android)](../quickstarts/get-started-unity-android.md)

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
