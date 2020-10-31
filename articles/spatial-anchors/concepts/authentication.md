---
title: Ověřování a autorizace
description: Seznamte se s různými způsoby, jak se aplikace nebo služba může ověřit pro prostorové kotvy Azure, a úrovně řízení, které musíte mít v bráně přístup k prostorovým ukotvením.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 12f9a91995eb35fa61a7df5f3ead5255aea0f071
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089028"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Ověřování a autorizace pro prostorové kotvy Azure

V tomto článku se seznámíte s různými způsoby, kterými se můžete z vaší aplikace nebo webové služby ověřit pro prostorové kotvy Azure. Dozvíte se taky o způsobech použití řízení přístupu na základě role Azure (RBAC) v Azure Active Directory (Azure AD) k řízení přístupu k vašim účtům prostorových kotev.

## <a name="overview"></a>Přehled

![Diagram, který zobrazuje přehled ověřování pro prostorové kotvy Azure.](./media/spatial-anchors-authentication-overview.png)

Aby klienti mohli získat přístup k danému účtu prostorových kotev Azure, musí nejdřív získat přístupový token ze služby tokenů zabezpečení (STS) ve službě Azure Mixed reality. Tokeny získané ze služby STS mají dobu života 24 hodin. Obsahují informace, které služba prostorových kotev používá k rozhodování o autorizaci na účtu a zajištění, aby k účtu měli přístup jenom autorizovaní objekty zabezpečení.

Přístupové tokeny se dají získat v Exchangi pro buď klíče účtu nebo tokeny vydané službou Azure AD.

Klíče účtu umožňují rychle začít pracovat s používáním služby prostorové kotvy Azure. Než nasadíte aplikaci do produkčního prostředí, doporučujeme, abyste aplikaci aktualizovali tak, aby používala ověřování Azure AD.

Ověřovací tokeny Azure AD můžete získat dvěma způsoby:

- Pokud vytváříte podnikovou aplikaci a vaše společnost používá jako svůj systém identit službu Azure AD, můžete v aplikaci použít ověřování Azure AD založené na uživateli. Potom udělíte přístup k účtům prostorových kotev pomocí stávajících skupin zabezpečení služby Azure AD. Přístup můžete také udělit přímo uživatelům ve vaší organizaci.
- V opačném případě doporučujeme získat tokeny Azure AD z webové služby, která podporuje vaši aplikaci. Tuto metodu doporučujeme pro produkční aplikace, protože umožňuje vyhnout se vkládání přihlašovacích údajů pro přístup k prostorovým kotvám Azure v klientské aplikaci.

## <a name="account-keys"></a>Klíče účtu

Nejjednodušší způsob, jak začít, je použití klíčů účtu pro přístup k vašemu účtu prostorových kotev Azure. Klíče účtu můžete získat na Azure Portal. Přejít na svůj účet a vybrat kartu **klíče** :

![Snímek obrazovky, který zobrazuje kartu klíče s vybraným tlačítkem kopírovat pro primární klíč](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

K dispozici jsou dva klíče. Obě jsou současně platné pro přístup k účtu prostorových kotev. Doporučujeme pravidelně aktualizovat klíč, který používáte pro přístup k účtu. Použití dvou samostatných platných klíčů umožňuje tyto aktualizace bez výpadků. Musíte taky aktualizovat primární klíč a sekundární klíč.

Sada SDK obsahuje integrovanou podporu pro ověřování prostřednictvím klíčů účtu. Pouze potřebujete nastavit `AccountKey` vlastnost `cloudSession` objektu:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Po nastavení této vlastnosti zpracuje sada SDK výměnu klíče účtu pro přístupový token a nezbytné ukládání tokenů do mezipaměti pro vaši aplikaci.

> [!WARNING]
> Pro rychlé zprovoznění doporučujeme používat klíče účtu, ale jenom během vývoje a vytváření prototypů. Nedoporučujeme dodávat aplikaci do produkčního klíče s vloženým klíčem účtu. Místo toho použijte přístup k ověřování Azure AD založenému na uživateli nebo službě na základě služby, které jsou popsané dál.

## <a name="azure-ad-user-authentication"></a>Ověřování uživatelů Azure AD

Pro aplikace, které cílí na Azure Active Directory uživatele, doporučujeme pro tohoto uživatele použít token Azure AD. Tento token můžete získat pomocí [MSAL](../../active-directory/develop/msal-overview.md). Postupujte podle kroků v [rychlém startu při registraci aplikace](../../active-directory/develop/quickstart-register-app.md), která zahrnuje:

**V Azure Portal**
1.    Zaregistrujte svoji aplikaci ve službě Azure AD jako nativní aplikaci. V rámci registrace budete muset určit, jestli má být vaše aplikace víceklientské. Také budete muset zadat adresy URL pro přesměrování povolené pro vaši aplikaci.
1.  Přejít na kartu **oprávnění rozhraní API** .
2.  Vyberte **Přidat oprávnění** .
    1.  Vyberte **poskytovatele prostředků hybridní reality** v **rozhraních API moje organizace používá** kartu.
    2.  Vyberte **delegovaná oprávnění** .
    3.  Vyberte **mixedreality.** Přihlaste se pod **mixedreality** .
    4.  Vyberte **Přidat oprávnění** .
3.  Vyberte **udělit souhlas správce** .
    
2. Udělit vaší aplikaci nebo uživatelům přístup k vašemu prostředku:
   1.    V Azure Portal přejít na prostředek prostorových ukotvení.
   2.    Přejděte na kartu **řízení přístupu (IAM)** .
   3.    Vyberte **Přidat přiřazení role** .
   1.    [Vyberte roli](#azure-role-based-access-control).
   2.    Do pole **Vybrat** zadejte jména uživatelů, skupin nebo aplikací, ke kterým chcete přiřadit přístup.
   3.    Vyberte **Uložit** .

**Ve vašem kódu**
1.    Nezapomeňte použít ID aplikace a identifikátor URI pro přesměrování vlastní aplikace Azure AD pro **ID klienta** a parametry **RedirectUri** v MSAL.
2.    Nastavte informace o tenantovi:
        1.    Pokud vaše aplikace podporuje **jenom moji organizaci** , nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** . Například contoso.microsoft.com.
        2.    Pokud vaše aplikace podporuje **účty v jakémkoli organizačním adresáři** , nahraďte tuto hodnotu **organizacemi** .
        3.    Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele** , nahraďte tuto hodnotu **běžnými** .
3.    U žádosti o token nastavte **Rozsah** **na https://sts.mixedreality.azure.com//.default .** Tento obor oznamuje službě Azure AD, že vaše aplikace požaduje token pro službu tokenu zabezpečení (STS) pro Mixed reality.

Po dokončení tohoto postupu by vaše aplikace měla být schopná získat z MSAL tokenu služby Azure AD. Token Azure AD můžete nastavit jako `authenticationToken` v objektu konfigurace relace cloudu:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Ověřování služby Azure AD

Pro nasazení aplikací, které používají prostorové kotvy Azure do produkčního prostředí, doporučujeme použít back-end službu, která bude zprostředkovatelem požadovat ověřování. Tady je přehled tohoto procesu:

![Diagram, který poskytuje přehled o ověřování pro prostorové kotvy Azure.](./media/spatial-anchors-aad-authentication.png)

V tomto případě předpokládáme, že vaše aplikace používá svůj vlastní mechanismus k ověření své back-endové služby. (Například účet Microsoft, PlayFab, Facebook, Google ID nebo vlastní uživatelské jméno a heslo.)  Po ověření uživatelů do vaší back-endové služby může tato služba získat token Azure AD, vyměnit ho pro přístupový token pro prostorové kotvy Azure a vrátit ho zpátky do klientské aplikace.

Přístupový token Azure AD se načte přes [MSAL](../../active-directory/develop/msal-overview.md). Postupujte podle kroků v části [rychlý Start pro registraci aplikace](../../active-directory/develop/quickstart-register-app.md), které zahrnují:

**V Azure Portal**
1.    Registrace aplikace ve službě Azure AD:
        1.    V Azure Portal vyberte **Azure Active Directory** a pak vyberte **Registrace aplikací** .
        2.    Vyberte **Nová registrace** .
        3.    Zadejte název vaší aplikace, jako typ aplikace vyberte **Webová aplikace nebo rozhraní API** a zadejte adresu URL ověřování pro vaši službu. Vyberte **Vytvořit** .
4.    V aplikaci vyberte **Nastavení** a pak vyberte kartu **certifikáty a tajné klíče** . Vytvořte nový tajný klíč klienta, vyberte dobu trvání a pak vyberte **Přidat** . Nezapomeňte uložit tajnou hodnotu. Budete je muset zahrnout do kódu vaší webové služby.
2.    Udělte vaší aplikaci nebo uživatelům přístup k vašemu prostředku:
        1.    V Azure Portal přejít na prostředek prostorových ukotvení.
        2.    Přejděte na kartu **řízení přístupu (IAM)** .
        3.    Vyberte **Přidat přiřazení role** .
        1.    [Vyberte roli](#azure-role-based-access-control).
        2.    Do pole **Vybrat** zadejte název nebo názvy aplikací, ke kterým chcete přiřadit přístup. Pokud chcete, aby uživatelé vaší aplikace měli různé role proti účtu prostorové kotvy, zaregistrujte několik aplikací v Azure AD a přiřaďte jim samostatnou roli. Potom implementujte logiku autorizace pro použití správné role pro vaše uživatele.
        
              > [!NOTE] 
              > V podokně **Přidat přiřazení role** v části **přiřazení přístupu k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD** .
    
      3.    Vyberte **Uložit** .
    
**Ve vašem kódu** 

>[!NOTE] 
> Můžete použít ukázku služby, která je k dispozici na GitHubu.

1.    Nezapomeňte použít ID aplikace, tajný klíč aplikace a identifikátor URI přesměrování vlastní aplikace Azure AD jako **ID klienta** , **tajný klíč** a parametry **RedirectUri** v MSAL.
2.    Nastavte ID tenanta na vlastní ID tenanta Azure AD v parametru **autorita** v MSAL.
3.    U žádosti o token nastavte **Rozsah** **na https://sts.mixedreality.azure.com//.default .**

Po dokončení tohoto postupu může vaše back-end služba získat token Azure AD. Pak ho může vyměňovat pro token MR, který se vrátí zpátky klientovi. Použití tokenu Azure AD k načtení tokenu MR se provádí prostřednictvím volání REST. Zde je příklad volání:

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

Autorizační hlavička je formátována takto: `Bearer <Azure_AD_token>`

Odpověď obsahuje token MR v prostém textu.

Tento token MR se pak vrátí klientovi. Klientská aplikace se pak může v konfiguraci cloudové relace nastavit jako přístupový token:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Řízení přístupu na základě role v Azure

Abychom vám pomohli řídit úroveň přístupu udělených aplikacím, službám nebo uživatelům Azure AD vaší služby, můžete těmto již existujícím rolím přiřadit účty prostorových kotev Azure:

- **Vlastník účtu prostorových ukotvení** . Aplikace nebo uživatelé, kteří mají tuto roli, mohou vytvářet prostorové kotvy, dotazovat se na ně a odstraňovat je. Při ověřování na účet pomocí klíčů účtu se k ověřenému objektu zabezpečení přiřadí role vlastníka účtu prostorových ukotvení.
- **Přispěvatel účtu prostorových kotev** Aplikace nebo uživatelé, kteří mají tuto roli, mohou vytvářet prostorové kotvy a dotazovat se na ně, ale nemůžou je odstranit.
- **Čtečka účtu prostorových kotev** Aplikace nebo uživatelé, kteří mají tuto roli, se mohou dotazovat pouze na prostorové kotvy. Nemůžou vytvářet nové, odstraňovat existující nebo aktualizovat metadata. Tato role se obvykle používá pro aplikace, u kterých někteří uživatelé nastavili prostředí, ale jiné mohou volat pouze kotvy dříve umístěné v prostředí.

## <a name="next-steps"></a>Další kroky

Vytvoření první aplikace pomocí prostorových kotev Azure:

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
