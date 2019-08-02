---
title: Ověřování a autorizace pro prostorové kotvy Azure | Microsoft Docs
description: Seznamte se s různými způsoby, jak se aplikace nebo služba může ověřit pro prostorové kotvy Azure, a úrovně řízení, které musíte mít v bráně přístup k prostorovým kotvám Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 850748462f0273f2dfb1522d900ce9f1b2156d2a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517067"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Ověřování a autorizace pro prostorové kotvy Azure

V této části se dozvíte o různých způsobech ověření pro prostorové kotvy Azure z vaší aplikace nebo webové služby a způsobů, jak můžete použít Access Control na základě rolí v adresáři Azure (Azure AD) k řízení přístupu k vašim účtům prostorových kotev.  

## <a name="overview"></a>Přehled

![Přehled ověřování pro prostorové kotvy Azure](./media/spatial-anchors-authentication-overview.png)

Aby klienti mohli získat přístup k danému účtu prostorových kotev Azure, musí nejdřív získat přístupový token ze služby tokenů zabezpečení (STS) ve službě Azure Mixed reality. Tokeny získané z služby STS Live po dobu 24 hodin a obsahují informace o službách prostorových ukotvení k tomu, aby mohl účet provádět autorizační rozhodnutí, a zajišťují, že k tomuto účtu budou mít přístup jenom autorizovaní objekty zabezpečení. 

Přístupové tokeny se dají získat v Exchangi buď z klíčů účtu, nebo z tokenů vydaných službou Azure AD. 

Klíče účtu umožňují rychle začít s používáním služby prostorových kotev Azure; před nasazením aplikace do produkčního prostředí však doporučujeme, abyste aplikaci aktualizovali tak, aby používala ověřování založené na službě Azure AD. 

Ověřovací tokeny Azure AD lze získat dvěma způsoby:

- Pokud vytváříte podnikovou aplikaci a vaše společnost používá jako svůj systém identit službu Azure AD, můžete v aplikaci použít ověřování Azure AD založené na uživateli a udělit přístup k účtům prostorových kotev pomocí stávajících skupin zabezpečení služby Azure AD nebo přímo uživatelům ve vaší organizaci. 
- V opačném případě se doporučuje získat tokeny Azure AD z webové služby, která podporuje vaši aplikaci. Použití podpůrné webové služby je doporučovanou metodou ověřování pro produkční aplikace, protože se vyhnete vložení přihlašovacích údajů pro přístup k prostorovým kotvám Azure v klientské aplikaci. 

## <a name="account-keys"></a>Klíče účtů

Nejjednodušší způsob, jak začít, je použití klíčů účtu pro přístup k účtu prostorových kotev Azure. Klíče účtu najdete na Azure Portal. Přejděte ke svému účtu a vyberte kartu klíče.

![Přehled ověřování pro prostorové kotvy Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


K dispozici jsou dva klíče, které jsou současně platné pro přístup k účtu prostorových kotev. Doporučuje se pravidelně aktualizovat klíč, který používáte pro přístup k účtu. Existují dva samostatné platné klíče, které tyto aktualizace umožňují bez výpadků; musíte aktualizovat jenom primární klíč a sekundární klíč. 

Sada SDK obsahuje integrovanou podporu pro ověřování pomocí klíčů účtu. jednoduše stačí nastavit vlastnost AccountKey objektu cloudSession. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Až to bude hotové, sada SDK bude pokládat s výměnou klíče účtu pro přístupový token a je nezbytné ukládat tokeny do mezipaměti pro vaši aplikaci. 

> [!WARNING] 
> Použití klíčů účtu se doporučuje pro rychlé zprovoznění, ale během vývoje a vytváření prototypů. Důrazně doporučujeme, abyste aplikaci nedodali do produkčního prostředí pomocí vloženého klíče účtu a místo toho používali přístup k ověřování Azure AD založeného na uživateli nebo službě na základě služby, které jsou uvedené dál.

## <a name="azure-ad-user-authentication"></a>Ověřování uživatelů Azure AD

U aplikací, které cílí na Azure Active Directory uživatele, se doporučuje použít pro uživatele token Azure AD, který můžete získat pomocí knihovny ADAL, jak je popsáno v následující dokumentaci: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); měli byste postupovat podle kroků uvedených v tomto tématu. v části Rychlé spuštění, mezi které patří:

1. Konfigurace v Azure Portal
    1.  Zaregistrujte svoji aplikaci ve službě Azure AD jako **nativní aplikaci**. V rámci registrace budete muset určit, jestli má aplikace více tenantů, nebo ne, a zadat adresy URL pro přesměrování povolené pro vaši aplikaci.  
    2.  Udělit vaší aplikaci nebo uživatelům přístup k vašemu prostředku: 
        1.  Přejděte ke zdroji prostorových kotev v Azure Portal
        2.  Přepnout na kartu **řízení přístupu (IAM)**
        3.  Kliknutí na **Přidat přiřazení role**
            1.  [Vybrat roli](#role-based-access-control)
            2.  Do pole **Vybrat** zadejte jména uživatelů, skupin nebo aplikací, ke kterým chcete přiřadit přístup, a jejich skupiny (y). 
            3.  Stiskněte **Uložit**.
2. V kódu:
    1.  Nezapomeňte použít **ID aplikace** a **identifikátor URI přesměrování** vlastní aplikace Azure AD jako **ID klienta** a parametry **RedirectUri** v ADAL.
    2.  Nastavte informace o tenantovi:
        1.  Pokud vaše aplikace podporuje **pouze moji organizaci**, nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
        2.  Pokud vaše aplikace podporuje **účty v jakémkoli organizačním adresáři**, nahraďte tuto hodnotu **organizacemi** .
        3.  Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele**, nahraďte tuto hodnotu **běžnými** .
    3.  U žádosti o token nastavte **prostředek** na https://sts.mixedreality.azure.com"". Tento prostředek bude označovat Azure AD, že vaše aplikace požaduje token pro službu Azure prostor kotev.  

V takovém případě by vaše aplikace měla být schopná získat z knihovny ADAL token Azure AD; Tento token Azure AD můžete nastavit jako **authenticationToken** v objektu konfigurace cloudové relace. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Ověřování služby Azure AD

Doporučená možnost nasazení aplikací využívajících prostorové kotvy Azure do produkčního prostředí je využití služby back-end, která bude zprostředkovatelem žádosti o ověření. Obecné schéma by mělo být popsané v tomto diagramu:

![Přehled ověřování pro prostorové kotvy Azure](./media/spatial-anchors-aad-authentication.png)

Tady se předpokládá, že vaše aplikace používá vlastní mechanismus (například: Účet Microsoft, PlayFab, Facebook, Google ID, vlastní uživatelské jméno a heslo atd.) ověřování pro službu back-end. Po ověření vašich uživatelů pro back-end službu může tato služba získat token Azure AD, vyměnit ho pro přístupový token pro prostorové kotvy Azure a vrátit ho zpátky do klientské aplikace.

Přístupový token Azure AD se načte pomocí knihovny ADAL, jak je popsáno v následující dokumentaci: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); měli byste postupovat podle kroků uvedených v části "rychlé starty", mezi které patří:

1.  Konfigurace v Azure Portal:
    1.  Registrace aplikace ve službě Azure AD:
        1.  V Azure Portal přejděte na **Azure Active Directory**a vyberte **Registrace aplikací** .
        2.  Vybrat **registraci nové aplikace**
        3.  Zadejte název vaší aplikace, jako typ aplikace vyberte **Webová aplikace nebo rozhraní API** a zadejte adresu URL ověřování pro vaši službu. Pak stiskněte **vytvořit**.
        4.  V této aplikaci klikněte na **Nastavení**a pak vyberte kartu **klíče** . Zadejte název klíče, vyberte dobu trvání a klikněte na **Uložit**. Nezapomeňte uložit hodnotu klíče, která se zobrazí v daném čase, protože ji budete muset zahrnout do kódu vaší webové služby.
    2.  Udělte vaší aplikaci nebo uživatelům přístup k vašemu prostředku:
        1.  Přejděte ke zdroji prostorových kotev v Azure Portal
        2.  Přepnout na kartu **řízení přístupu (IAM)**
        3.  Kliknutí na **Přidat přiřazení role**
        1.  [Vybrat roli](#role-based-access-control)
        2.  Do pole **Vybrat** zadejte název aplikace, kterou jste vytvořili a ke kterému chcete přiřadit přístup. Pokud chcete, aby uživatelé vaší aplikace měli různé role proti účtu prostorové kotvy, měli byste v Azure AD zaregistrovat několik aplikací a přiřadit je ke každé samostatné roli. Potom implementujte logiku autorizace pro použití správné role pro vaše uživatele.  
    3.  Stiskněte **Uložit**.
2.  V kódu (Poznámka: můžete použít ukázku služby, která je součástí GitHubu):
    1.  Nezapomeňte použít ID aplikace, tajný klíč aplikace a identifikátor URI pro přesměrování vlastní aplikace Azure AD jako ID klienta, tajný klíč a parametry RedirectUri v ADAL.
    2.  Nastavte ID tenanta na vlastní AAAzure přidání ID tenanta do parametru autorita v ADAL.
    3.  U žádosti o token nastavte **prostředek** na https://sts.mixedreality.azure.com"". 

V takovém případě může back-end služba získat token Azure AD. Pak ho může vyměňovat pro token MR, který se vrátí zpátky klientovi. Použití tokenu Azure AD k načtení tokenu MR se provádí prostřednictvím volání REST. Toto je ukázkové volání:

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

Záhlaví autorizace je formátováno takto:`Bearer <accoundId>:<accountKey>`

A odpověď obsahuje token MR v prostém textu.
 
Tento token MR se pak vrátí klientovi. Klientská aplikace se pak může v konfiguraci cloudové relace nastavit jako přístupový token.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

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

## <a name="next-steps"></a>Další postup

Vytvořte svou první aplikaci pomocí prostorových kotev Azure.

> [!div class="nextstepaction"]
> [Jednot](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
