---
title: Ověřování a autorizace na prostorový ukotvení Azure | Dokumentace Microsoftu
description: Další informace o různých způsobech, který může ověřit aplikaci nebo službě prostorových kotev vztahů Azure a úrovní ovládací prvek, který budete muset bránu přístup k Azure prostorových kotvy.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: c7ffa432c9311ba9d4ecf4ba82c375e2dad988d0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478545"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Ověřování a autorizace na Azure prostorových ukotvení

V této části se probereme různé způsoby, můžete se ověřit na prostorový ukotvení Azure z vaší aplikace nebo webové služby a způsoby, ve kterém můžete použít řízení přístupu na základě Role ve službě Azure Directory (Azure AD) pro řízení přístupu k účtům prostorových ukotvení.  

## <a name="overview"></a>Přehled

![Přehled ověřování na Azure prostorových ukotvení](./media/spatial-anchors-authentication-overview.png)

Pro přístup k dané účtu Azure prostorových ukotvení, klienti musí nejprve získání přístupového tokenu z Azure smíšené Reality službu tokenů zabezpečení (STS). Tokeny získat ze služby STS live po dobu 24 hodin a obsahují informace pro služby prostorových kotvy na rozhodnutí o autorizaci na účtu a ujistěte se, že jenom autorizované objekty zabezpečení můžete přístup k tomuto účtu. 

Přístupové tokeny lze získat v systému exchange z obou klíčů účtu, nebo z Azure AD vydané tokeny. 

Klíče účtu umožňují rychle začít s pomocí služby Azure prostorových kotvy; ale předtím, než nasadíte svou aplikaci do produkčního prostředí, doporučujeme aktualizovat aplikace pro použití ověřování na základě AD Azure. 

Tokeny ověřování Azure AD můžete získat dvěma způsoby:

- Pokud vytváříte podniková aplikace a vaše společnost používá služby Azure AD jako jeho systému identit, můžete použít Azure založené na uživatelích ověřování AD v rámci vaší aplikace a udělit přístup k účtům prostorových ukotvení pomocí existujících skupin zabezpečení Azure AD, nebo přímo uživatelům ve vaší organizaci. 
- V opačném případě se doporučuje, získat tokeny služby Azure AD z webovou službu podporující vaši aplikaci. Pomocí podpory webové služby je doporučenou metodou ověřování pro produkční aplikace, protože tím eliminujete vložení přihlašovacích údajů pro přístup k Azure prostorových ukotvení v klientské aplikaci. 

## <a name="account-keys"></a>Klíče účtu

Pomocí klíčů účtu pro přístup k vašemu účtu Azure prostorových kotvy je nejjednodušší způsob, jak začít pracovat. Klíče účtu najdete na webu Azure portal. Přejděte do svého účtu a vyberte kartu "Klíče".

![Přehled ověřování na Azure prostorových ukotvení](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Dva klíče jsou k dispozici, obě jsou současně platná pro přístup k účtu prostorových kotvy. Doporučujeme pravidelně aktualizovat klíč, který používáte k přístupu k účtu. dva samostatné platný klíčů povolit, aktualizuje bez výpadků, s stačí aktualizovat také primární klíč a sekundární klíč. 

Sada SDK obsahuje integrovanou podporu pro ověřování pomocí klíče účtu; Stačí nastavit vlastnost AccountKey cloudSession objektu. 

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

# <a name="c-ndktabcpp"></a>[C++SADA NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

***

Po dokončení, sada SDK zajistí výměny klíče účtu pro přístupový token a nezbytné ukládání do mezipaměti tokenů pro vaši aplikaci. 

> [!WARNING] 
> Pro rychlé zprovoznění, ale během vývoje a vytváření prototypů pouze se doporučuje použít klíče účtu. Důrazně doporučujeme není k odeslání aplikace do produkčního prostředí v klíče vložený účtu služby a místo toho použití založené na uživatelích nebo založené na službě Azure AD authentication blíží uvedené další.

## <a name="azure-ad-user-authentication"></a>Ověřování uživatelů Azure AD

Pro aplikace zaměřená na uživatele v Azure Active Directory, je doporučený postup pro použití tokenu Azure AD pro uživatele, který můžete získat pomocí knihovny ADAL, jak je popsáno v následující dokumentaci: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); je postupujte podle kroků uvedených v části "Rychlého spuštění", mezi které patří:

1. Konfigurace webu Azure portal
    1.  Registrace vaší aplikace ve službě Azure AD jako **nativní aplikace**. Jako součást registrace budete muset zjistit, jestli vaše aplikace by měla být více tenantů nebo Ne a poskytují přesměrování, na které povolené adresy URL pro vaši aplikaci.  
    2.  Udělte přístup aplikaci nebo uživatele na váš prostředek: 
        1.  Přejděte do prostředku prostorových kotvy na webu Azure portal
        2.  Přepněte **řízení přístupu (IAM)** kartu
        3.  Spuštění **přidat přiřazení role**
            1.  [Vyberte roli](#role-based-access-control)
            2.  V **vyberte** zadejte jméno uživatele, skupiny nebo aplikace, ke kterému chcete přiřadit přístup. 
            3.  Spuštění **Uložit**.
2. Ve vašem kódu:
    1.  Je nutné použít **ID aplikace** a **identifikátor Uri pro přesměrování** vlastní aplikace Azure AD jako **ID klienta** a **identifikátor URI pro přesměrování** Parametry v ADAL
    2.  Nastavte informace o tenantovi:
        1.  Pokud vaše aplikace podporuje **Moje organizace jenom**, nahradí tato hodnota se vaše **ID Tenanta** nebo **název Tenanta** (například contoso.microsoft.com)
        2.  Pokud vaše aplikace podporuje **účty v libovolném adresáři organizace**, nahradí tato hodnota se **organizace**
        3.  Pokud vaše aplikace podporuje **uživatele účtu Microsoft všechny**, nahradí tato hodnota se **Common**
    3.  Na vaši žádost o token nastavte **prostředků** na "https://sts.mixedreality.azure.com". Tento "prostředek" označí do služby Azure AD, že vaše aplikace žádá token pro službu Azure prostorových ukotvení.  

S velkou vaší aplikace měli být schopni získat z ADAL tokenu Azure AD; Tento token Azure AD jako můžete nastavit **authenticationToken** na objekt konfigurace relace cloudu. 

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

# <a name="c-ndktabcpp"></a>[C++SADA NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

***

## <a name="azure-ad-service-authentication"></a>Ověřování služby Azure AD

Doporučená možnost nasazovat aplikace s využitím prostorových kotvy Azure do produkčního prostředí je využívat back-end službu, která bude zprostředkovatel požadavků na ověření. Obecné schéma by mělo být, jak je popsáno v tomto diagramu:

![Přehled ověřování na Azure prostorových ukotvení](./media/spatial-anchors-aad-authentication.png)

Tady, se předpokládá, že vaše aplikace používá vlastní mechanismus (například: Účet Microsoft, PlayFab, Facebook, Google s ID, vlastního uživatelského jména a hesla atd.) k ověření svůj back-end služby. Po ověření uživatele do služby back-end, který služba získat token Azure AD, výměně pro přístupový token Azure prostorových ukotvení a vrátit zpět do klientské aplikace.

Přístupový token Azure AD jsou načítány s použitím knihovny ADAL, jak je popsáno v následující dokumentaci: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); postupujte podle kroků uvedených v části "Rychlého spuštění", mezi které patří:

1.  Konfigurace webu Azure portal:
    1.  Registrace vaší aplikace ve službě Azure AD:
        1.  Na webu Azure portal, přejděte na **Azure Active Directory**a vyberte **registrace aplikací**
        2.  Vyberte **registrace nové aplikace**
        3.  Zadejte název aplikace, vyberte **webovou aplikaci nebo API** jako typ aplikace a zadejte adresu URL vícefaktorového ověřování pro vaši službu. Pak klikněte na tlačítko **vytvořit**.
        4.  Na tuto aplikaci přístupů **nastavení**, vyberte **klíče** kartu. Zadejte název klíče, vyberte dobu trvání a přístupů **Uložit**. Ujistěte se, zda hodnota klíče, který se zobrazí v té době si uložte, protože je potřeba zahrnout do kódu webové služby.
    2.  Udělte vaší aplikace a/nebo uživatelům přístup k prostředku:
        1.  Přejděte do prostředku prostorových kotvy na webu Azure portal
        2.  Přepněte **řízení přístupu (IAM)** kartu
        3.  Spuštění **přidat přiřazení role**
        1.  [Vyberte roli](#role-based-access-control)
        2.  V **vyberte** pole, zadejte název aplikace, které jste vytvořili a do které chcete přiřadit přístup. Pokud chcete uživatelům vaší aplikace mají rozdílné role prostorových kotvy účtu, doporučujeme zaregistrovat více aplikací ve službě Azure AD a přiřadit každé samostatné role. Pak implementujte logiku autorizace používat vhodnou roli pro uživatele.  
    3.  Spuštění **Uložit**.
2.  Ve vašem kódu (Poznámka: můžete použít na služby ukázku na Githubu):
    1.  Nezapomeňte použít ID aplikace, tajný klíč aplikace a identifikátor Uri služby Azure AD aplikace jako ID klienta přesměrování tajný klíč a identifikátor URI pro přesměrování parametry ADAL
    2.  Nastavte ID tenanta na vlastní AAAzure přidat ID tenanta v parametru autorita v ADAL
    3.  Na vaši žádost o token nastavte **prostředků** na "https://sts.mixedreality.azure.com" 

S tím můžete načíst váš back-end službu tokenu Azure AD. To je pak exchange pro MR token, který vrátí zpět do klienta. Pomocí tokenu Azure AD k načtení tokenu MR se provádí prostřednictvím volání REST. Tady je ukázka volání:

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

Kde autorizační hlavičky naformátován takto: `Bearer <accoundId>:<accountKey>`

A odpovědi obsahuje token MR ve formátu prostého textu.
 
Tento token MR je pak vrácen do klienta. Klientská aplikace pak můžete nastavit ho jako jeho přístupového tokenu v konfiguraci relace cloudu.

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

# <a name="c-ndktabcpp"></a>[C++SADA NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

***

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Které pomáhá řídit udělená úroveň přístupu do aplikací, služeb nebo uživatelů Azure AD vaší služby přiřazení podle potřeby proti účtům Azure prostorových kotvy byly vytvořeny následující role:

- **Prostorové vlastník účtu kotvy**: aplikace nebo uživatele, kteří mají tuto roli budou moct prostorových ukotvení vytvářet, dotazovat na jejich a je odstranit. Při ověřování k vašemu účtu pomocí klíče účtu **prostorových vlastník účtu kotvy** role je přiřazená ověřený objekt zabezpečení. 
- **Přispěvatel účtů prostorových kotvy**: aplikace nebo uživatele, kteří mají tuto roli budou moct vytvořit prostorových kotvy dotazu, ale nelze je odstranit. 
- **Prostorový kotvy účet čtečky**: aplikace nebo uživatele, kteří mají tuto roli mohou pouze dotazování prostorových ukotvení, ale nelze vytvořit nové, odstranit existující balíčky, a aktualizaci metadat na prostorový kotvy. To se obvykle používá pro aplikace, kdy někteří uživatelé kurátorovat prostředí, zatímco ostatní pouze odvolat kotvy dříve umístili v daném prostředí.

## <a name="next-steps"></a>Další postup

Vytvoření první aplikace pomocí Azure prostorových ukotvení.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
