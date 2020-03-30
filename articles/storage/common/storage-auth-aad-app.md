---
title: Získání tokenu z Azure AD pro autorizaci požadavků z klientské aplikace
titleSuffix: Azure Storage
description: Pomocí služby Azure Active Directory se můžete ověřovat v rámci klientské aplikace, získat token OAuth 2.0 a autorizovat požadavky do úložiště objektů blob Azure a úložiště front.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268480"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Získání tokenu z Azure AD pro autorizaci požadavků z klientské aplikace

Klíčovou výhodou používání Azure Active Directory (Azure AD) s úložištěm objektů blob Azure nebo úložištěm fronty je, že vaše přihlašovací údaje už nemusí být uloženy ve vašem kódu. Místo toho můžete požádat o přístupový token OAuth 2.0 z platformy identit Microsoftu (dříve Azure AD). Azure AD ověřuje intezuru zabezpečení (uživatele, skupiny nebo instančního objektu) spuštěné aplikace. Pokud ověřování proběhne úspěšně, Azure AD vrátí přístupový token do aplikace a aplikace pak můžete použít přístupový token k autorizaci požadavků do úložiště objektů blob Azure nebo úložiště fronty.

Tento článek ukazuje, jak nakonfigurovat nativní aplikaci nebo webovou aplikaci pro ověřování pomocí platformy Microsoft Identity Platform 2.0. Příklad kódu obsahuje rozhraní .NET, ale jiné jazyky používají podobný přístup. Další informace o platformě Microsoft Identity Platform 2.0 naleznete [v přehledu platformy microsoftidentit (v2.0).](../../active-directory/develop/v2-overview.md)

Přehled toku udělení kódu OAuth 2.0 najdete v tématu [Autorizace přístupu k webovým aplikacím Služby Azure Active Directory pomocí toku udělení kódu OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Přiřazení role k objektu zabezpečení Azure AD

Chcete-li ověřit objekt zabezpečení z aplikace Azure Storage, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí (RBAC) pro tento objekt zabezpečení. Azure Storage definuje předdefinované role RBAC, které zahrnují oprávnění pro kontejnery a fronty. Pokud je role RBAC přiřazena k objektu zabezpečení, je tomuto objektu zabezpečení udělen přístup k tomuto prostředku. Další informace naleznete v [tématu Správa přístupových práv k datům objektů blob a fronty Azure pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace u klienta Azure AD

Prvním krokem při používání Azure AD k autorizaci přístupu k prostředkům úložiště je registrace klientské aplikace s klientem Azure AD z [webu Azure Portal](https://portal.azure.com). Při registraci klientské aplikace zadáte informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID klienta (také volal *ID aplikace),* které používáte k přidružení aplikace k Azure AD za běhu. Další informace o ID klienta najdete v [tématu Objekty za registrovaných aplikací a služeb ve službě Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat aplikaci Azure Storage, postupujte podle kroků uvedených na [úvodním panelu: Zaregistrujte aplikaci na platformě microsoft identity](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Následující obrázek znázorňuje běžné nastavení pro registraci webové aplikace:

![Snímek obrazovky znázorňující, jak zaregistrovat aplikaci úložiště ve službě Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Pokud zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI přesměrování**. Pro nativní aplikace tato hodnota nemusí být skutečná adresa URL. U webových aplikací musí být identifikátor URI přesměrování platný identifikátor URI, protože určuje adresu URL, na kterou jsou k dispozici tokeny.

Po registraci aplikace se v části Nastavení zobrazí ID aplikace (nebo ID **klienta):**

![Snímek obrazovky s ID klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Další informace o registraci aplikace ve službě Azure AD najdete v [tématu Integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Udělení oprávnění registrované aplikace službě Azure Storage

Dále udělte oprávnění k aplikacím pro volání api úložiště Azure. Tento krok umožňuje vaší aplikaci autorizovat požadavky na Azure Storage s Azure AD.

1. Na stránce **Přehled** registrované aplikace vyberte **Zobrazit oprávnění rozhraní API**.
1. V části **Oprávnění rozhraní API** vyberte Přidat **oprávnění** a zvolte rozhraní **Microsoft API**.
1. Vyberte **Azure Storage** ze seznamu výsledků, chcete-li zobrazit podokno **oprávnění Rozhraní API požadavku.**
1. V části **Jaký typ oprávnění aplikace vyžaduje?**, všimněte si, že dostupným typem oprávnění jsou oprávnění **Delegovaná**. Tato možnost je vybrána ve výchozím nastavení.
1. V části **Vybrat oprávnění** v podokně **Požádat o oprávnění rozhraní API** zaškrtněte políčko vedle **user_impersonation**a klepněte na tlačítko **Přidat oprávnění**.

    ![Snímek obrazovky s oprávněními pro úložiště](media/storage-auth-aad-app/registered-app-permissions-1.png)

Podokno **oprávnění rozhraní API** teď ukazuje, že vaše registrovaná aplikace Azure AD má přístup k Microsoft Graphu i k Úložišti Azure. Oprávnění se microsoft graphu udělují automaticky při první registraci aplikace ve službě Azure AD.

![Snímek obrazovky s oprávněními registrace aplikací](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Aplikace potřebuje tajný klíč klienta k prokázání své identity při požadování tokenu. Chcete-li přidat tajný klíč klienta, postupujte takto:

1. Přejděte na registraci aplikace na webu Azure Portal.
1. Vyberte nastavení **Certifikáty & tajných kódů.**
1. V části **Tajné klíče klienta**klepněte na tlačítko **Nový tajný klíč klienta** a vytvořte nový tajný klíč.
1. Zadejte popis tajného klíče a zvolte požadovaný interval vypršení platnosti.
1. Okamžitě zkopírujte hodnotu nového tajného klíče do zabezpečeného umístění. Plná hodnota se zobrazí pouze jednou.

    ![Snímek obrazovky s tajným tajemstvím klienta](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získávání tokenů

Jakmile zaregistrujete aplikaci a udělíte jí oprávnění k přístupu k datům v úložišti objektů Blob Azure nebo ve frontě, můžete do aplikace přidat kód k ověření objektu zabezpečení a získat token OAuth 2.0. K ověření a získání tokenu můžete použít jednu z [knihoven ověřování platformy identit Microsoftu](../../active-directory/develop/reference-v2-libraries.md) nebo jinou otevřenou knihovnu, která podporuje OpenID Connect 1.0. Vaše aplikace pak můžete použít přístupový token k autorizaci požadavku proti úložišti objektů blob Azure nebo fronty úložiště.

Seznam scénářů, pro které je podporováno získávání tokenů, naleznete v části [toků ověřování](/en-us/azure/active-directory/develop/msal-authentication-flows) [obsahu knihovny ověřování společnosti Microsoft](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Známé hodnoty pro ověřování pomocí Azure AD

Chcete-li ověřit objekt zabezpečení pomocí služby Azure AD, musíte do kódu zahrnout některé známé hodnoty.

### <a name="azure-ad-authority"></a>Autorita Azure AD

Pro veřejný cloud Microsoftu je základní autorita Azure AD následující, kde *id klienta* je vaše ID klienta služby Active Directory (nebo ID adresáře):

`https://login.microsoftonline.com/<tenant-id>/`

ID klienta identifikuje klienta Azure AD, který se má použít pro ověřování. Označuje se také jako ID adresáře. Pokud chcete načíst ID klienta, přejděte na stránku **Přehled** pro registraci aplikace na webu Azure Portal a zkopírujte hodnotu odtud.

### <a name="azure-storage-resource-id"></a>ID prostředků úložiště Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu rozhraní .NET: Vytvoření objektu blob bloku

Příklad kódu ukazuje, jak získat přístupový token z Azure AD. Přístupový token se používá k ověření zadaného uživatele a potom autorizovat požadavek na vytvoření objektu blob bloku. Chcete-li tuto ukázku zpracovat, postupujte nejprve podle kroků popsaných v předchozích částech.

Chcete-li požádat o token, budete potřebovat následující hodnoty z registrace aplikace:

- Název domény Azure AD. Tuto hodnotu načtěte ze stránky **Přehled** služby Azure Active Directory.
- ID klienta (nebo adresáře). Tuto hodnotu načtěte na stránce **Přehled** registrace aplikace.
- ID klienta (nebo aplikace). Tuto hodnotu načtěte na stránce **Přehled** registrace aplikace.
- Identifikátor URI přesměrování klienta. Načtěte tuto hodnotu z nastavení **ověřování** pro registraci aplikace.
- Hodnota tajného klíče klienta. Načtěte tuto hodnotu z umístění, do kterého jste ji dříve zkopírovali.

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Chcete-li spustit ukázku kódu, vytvořte účet úložiště v rámci stejného předplatného jako azure active directory. Pak vytvořte kontejner v rámci tohoto účtu úložiště. Ukázkový kód vytvoří objekt blob bloku v tomto kontejneru.

Dále explicitně přiřaďte roli **přispěvatele dat objektů blob úložiště** k uživatelskému účtu, pod kterým spustíte ukázkový kód. Pokyny, jak přiřadit tuto roli na webu Azure Portal, najdete v tématu [Udělení přístupu k datům objektů blob Azure a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Když vytvoříte účet Azure Storage, nebudou automaticky přiřazena oprávnění k přístupu k datům prostřednictvím Azure AD. Musíte explicitně přiřadit sami roli RBAC pro Azure Storage. Můžete ji přiřadit na úrovni předplatného, skupiny prostředků, účtu úložiště nebo kontejneru nebo fronty.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Vytvoření webové aplikace, která autorizuje přístup k úložišti objektů Blob pomocí Azure AD

Když vaše aplikace přistupuje k Azure Storage, činí tak jménem uživatele, což znamená, že prostředky objektu blob nebo fronty jsou přístupné pomocí oprávnění uživatele, který je přihlášen. Chcete-li vyzkoušet tento příklad kódu, budete potřebovat webovou aplikaci, která vyzve uživatele k přihlášení pomocí identity Azure AD. Můžete vytvořit vlastní nebo použít ukázkovou aplikaci poskytovanou společností Microsoft.

Dokončená ukázková webová aplikace, která získá token a použije ho k vytvoření objektu blob ve službě Azure Storage, je dostupná na [GitHubu](https://aka.ms/aadstorage). Kontrola a spuštění dokončené ukázky může být užitečné pro pochopení příkladů kódu. Pokyny k spuštění dokončené ukázky naleznete v části s názvem [Zobrazení a spuštění dokončené ukázky](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Přidání odkazů a použití příkazů  

Z Visual Studia nainstalujte klientskou knihovnu Azure Storage. V nabídce **Nástroje** vyberte **Správce balíčků NuGet** a potom **Konzola Správce balíčků**. Zadejte do okna konzoly následující příkazy pro instalaci potřebných balíčků z klientské knihovny Úložiště Azure pro rozhraní .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Dále přidejte do souboru HomeController.cs následující příkazy pomocí:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Vytvoření objektu blob bloku

Přidejte následující fragment kódu a vytvořte objekt blob bloku:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Chcete-li autorizovat operace objektů blob a fronty pomocí tokenu OAuth 2.0, musíte použít protokol HTTPS.

Ve výše uvedeném příkladu klientská knihovna .NET zpracovává autorizaci požadavku na vytvoření objektu blob bloku. Klientské knihovny Azure Storage pro jiné jazyky také zpracovávají autorizaci požadavku za vás. Pokud však voláte operaci úložiště Azure s tokenem OAuth pomocí rozhraní REST API, budete muset požadavek autorizovat pomocí tokenu OAuth.

Chcete-li volat operace služby Objektů blob a fronty pomocí přístupových tokenů OAuth, předejte přístupový token v hlavičce **Autorizace** pomocí schématu **Nosič** a zadejte verzi služby 2017-11-09 nebo vyšší, jak je znázorněno v následujícím příkladu:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Získání tokenu OAuth z Azure AD

Dále přidejte metodu, která požaduje token z Azure AD jménem uživatele. Tato metoda definuje obor, pro který mají být udělena oprávnění. Další informace o oprávněních a oborech naleznete [v tématu Oprávnění a souhlas v koncovém bodě platformy identit microsoftu](../../active-directory/develop/v2-permissions-and-consent.md).

ID prostředku použijte k vytvoření oboru, pro který chcete získat token. Příklad vytvoří obor pomocí ID prostředku spolu s předdefinovaným `user_impersonation` oborem, což znamená, že token je požadován jménem uživatele.

Mějte na paměti, že budete muset předložit uživateli rozhraní, které umožňuje uživateli souhlas požádat o token jejich jménem. Pokud je nutný souhlas, příklad zachytí **MsalUiRequiredException** a volá jinou metodu pro usnadnění žádosti o souhlas:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Souhlas je proces, kdy uživatel udělí oprávnění k aplikaci přístupu k chráněným prostředkům jejich jménem. Platforma identit společnosti Microsoft 2.0 podporuje přírůstkový souhlas, což znamená, že objekt zabezpečení může nejprve požadovat minimální sadu oprávnění a podle potřeby přidávat oprávnění v průběhu času. Když váš kód požaduje přístupový token, zadejte rozsah oprávnění, která vaše `scope` aplikace potřebuje v daném okamžiku v parametru. Další informace o přírůstkovém souhlasu naleznete v části **Přírůstkový a dynamický souhlas** v části [Proč aktualizovat na platformu identit microsoftu (v2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Následující metoda vytváří vlastnosti ověřování pro vyžádání přírůstkového souhlasu:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Zobrazení a spuštění dokončené ukázky

Chcete-li spustit ukázkovou aplikaci, nejprve klonovat nebo stáhnout z [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Potom aktualizujte aplikaci, jak je popsáno v následujících částech.

### <a name="provide-values-in-the-settings-file"></a>Zadání hodnot v souboru nastavení

Dále aktualizujte soubor *appsettings.json* vlastními hodnotami takto:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Aktualizace účtu úložiště a názvu kontejneru

V *souboru HomeController.cs* aktualizujte identifikátor URI, který odkazuje na objekt blob bloku, a použijte název účtu úložiště a kontejneru:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Povolit implicitní tok grantů

Chcete-li spustit ukázku, budete muset nakonfigurovat implicitní tok udělení pro registraci aplikace. Postupujte následovně:

1. Přejděte na registraci aplikace na webu Azure Portal.
1. V části Spravovat vyberte nastavení **Ověřování.**
1. V části **Upřesnit nastavení**zaškrtněte v části **Implicitní udělení** políčka, chcete-li povolit přístupové tokeny a tokeny ID, jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky znázorňující, jak povolit nastavení pro implicitní tok grantů](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Aktualizace portu používaného localhost

Při spuštění ukázky, můžete zjistit, že budete muset aktualizovat identifikátor URI přesměrování zadaný v registraci aplikace použít port *localhost* přiřazený za běhu. Chcete-li aktualizovat identifikátor URI přesměrování tak, aby používal přiřazený port, postupujte takto:

1. Přejděte na registraci aplikace na webu Azure Portal.
1. V části Spravovat vyberte nastavení **Ověřování.**
1. V části **Přesměrovat identifikátory URI**upravte port tak, aby odpovídal portu používanému ukázkovou aplikací, jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky s identifikátory URI přesměrování pro registraci aplikace](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Další kroky

- Další informace o platformě microsoftidentity najdete v [tématu Platform identity Microsoftu](https://docs.microsoft.com/azure/active-directory/develop/).
- Další informace o rolích RBAC pro úložiště Azure najdete v [tématu Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Informace o používání spravovaných identit pro prostředky Azure pomocí Azure Storage najdete v [tématu Ověřování přístupu k objektům BLOB a front pomocí Služby Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md).
