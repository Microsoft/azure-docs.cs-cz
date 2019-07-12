---
title: Ověřování pomocí Azure Active Directory pro přístup k datům objektu blob a fronty z klientské aplikace
description: Pomocí Azure Active Directory k ověřování z v rámci klientské aplikace, získání tokenu OAuth 2.0 a autorizaci požadavků na úložiště Azure Blob storage a Queue storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: da10b70b85e284173abbd1779fb1d39f477ca0cd
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723227"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Ověřování pomocí Azure Active Directory z aplikace pro přístup k objektům BLOB a fronty

Klíčovou výhodou Azure Blob storage nebo Queue storage pomocí Azure Active Directory (Azure AD) je, že vaše přihlašovací údaje už nemusí být uloženy ve vašem kódu. Přístupový token OAuth 2.0 můžete místo toho si vyžádat od platforma identit Microsoft (dříve Azure AD). Služba Azure AD ověřuje objektu zabezpečení (uživatele, skupiny nebo instanční objekt služby) spuštění aplikace. Pokud je ověřování úspěšné, Azure AD vrací přístupový token k aplikaci a aplikace můžete potom použít přístupový token k autorizaci požadavků na úložiště Azure Blob storage nebo Queue storage.

Tento článek ukazuje postup při konfiguraci webové aplikace k ověřování vaší nativní aplikace s platformou identity Microsoft 2.0. Funkce .NET příklad kódu, ale jiných jazycích používat podobný přístup. Další informace o Microsoft identity platform 2.0 najdete v tématu [Microsoft identity platform (v2.0) přehled](../../active-directory/develop/v2-overview.md).

Přehled toku přidělení kódu OAuth 2.0, naleznete v tématu [autorizovat přístup k Azure Active Directory webovým aplikacím pomocí OAuth 2.0 kódu udělit tok](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Přiřazení role zabezpečení služby Azure AD instančního objektu

K ověření objektu zabezpečení z vaší aplikace Azure Storage, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí pro tento objekt zabezpečení. Azure Storage definuje předdefinované role RBAC, které zahrnuje oprávnění pro kontejnery a fronty. Pokud RBAC role je přiřazena k objektu zabezpečení, tento objekt zabezpečení se udělí přístup k prostředku. Další informace najdete v tématu [Správa přístupových práv k datům objektů Blob v Azure a fronty pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta služby Azure AD

Prvním krokem při používání služby Azure AD k autorizaci přístupu k prostředkům úložiště je registraci tenanta Azure AD z klientské aplikace [webu Azure portal](https://portal.azure.com). Když si zaregistrujete klientské aplikace, je třeba zadat informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID klienta (také nazývané *ID aplikace*), který používáte k aplikaci přidružit k Azure AD za běhu. Další informace o ID klienta najdete v tématu [aplikace a instanční objekty v Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat aplikaci Azure Storage, postupujte podle kroků je znázorněno v [rychlý start: Registrace aplikace s platformou identity Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Obecná nastavení pro registraci webové aplikace na následujícím obrázku:

![Snímek obrazovky ukazující, jak zaregistrovat svoji aplikaci storage v Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Když si zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI pro přesměrování**. Pro nativní aplikace tato hodnota nemá být skutečná adresa URL. Pro webové aplikace identifikátor URI pro přesměrování musí být platný identifikátor URI, protože určuje adresu URL, ke které jsou k dispozici tokeny.

Po zaregistrování vaší aplikace, zobrazí se vám ID aplikace (nebo ID klienta) v části **nastavení**:

![Snímek obrazovky s ID klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Registrované aplikaci udělit oprávnění ke službě Azure Storage

V dalším kroku udělení oprávnění aplikace volat rozhraní API služby Azure Storage. Tento krok umožňuje vaší aplikaci k autorizaci požadavků ve službě Azure Storage s Azure AD.

1. Na **přehled** stránka pro registraci aplikace, vyberte **oprávnění k zobrazení rozhraní API**.
1. V **oprávnění k rozhraní API** vyberte **přidat oprávnění** a zvolte **Microsoft APIs**.
1. Vyberte **služby Azure Storage** ze seznamu výsledků k zobrazení **žádosti rozhraní API oprávnění** podokně.
1. V části **jaký typ oprávnění aplikace vyžaduje?** , podívejte se, že je k dispozici oprávnění typu **delegovaná oprávnění**. Ve výchozím nastavení je vybraná tato možnost za vás.
1. V **vyberte oprávnění** část **žádosti rozhraní API oprávnění** podokně zaškrtněte políčko vedle položky **user_impersonation**, pak klikněte na tlačítko **přidat oprávnění**.

    ![Snímek obrazovky znázorňující oprávnění pro úložiště](media/storage-auth-aad-app/registered-app-permissions-1.png)

**Oprávnění k rozhraní API** podokně nyní ukazuje, že vaše registrovaná aplikace Azure AD má přístup k Microsoft Graph a Azure Storage. Oprávnění jsou udělena pro Microsoft Graph automaticky při první registraci vaší aplikace s Azure AD.

![Snímek obrazovky registrace oprávnění aplikace](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Tajný kód klienta k prokázání své identity při vyžádání tokenu musí aplikace. Chcete-li přidat tajný klíč klienta, postupujte takto:

1. Přejděte do registrace vaší aplikace na webu Azure Portal.
1. Vyberte **certifikáty a tajné kódy** nastavení.
1. V části **tajné klíče klienta**, klikněte na tlačítko **nový tajný kód klienta** vytvořit nový tajný kód.
1. Zadejte popis pro tajný klíč a zvolte požadovaný vypršení intervalu.
1. Okamžitě zkopírujte hodnotu nový tajný kód do zabezpečeného umístění. Plný potenciál se zobrazí, jenom jednou.

    ![Tajný kód klienta snímek obrazovky znázorňující](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získání tokenu

Jakmile vaši aplikaci zaregistrovali a udělil jí oprávnění pro přístup k datům v Azure Blob storage nebo Queue storage, můžete přidat kód do vaší aplikace k ověření objektu zabezpečení a získání tokenu OAuth 2.0. K ověření a získání tokenu, můžete použít jednu ze [knihovny ověřování platforma identit Microsoft](../../active-directory/develop/reference-v2-libraries.md) nebo jiné knihovny open source, který podporuje OpenID Connect 1.0. Aplikace pak může použít přístupový token k autorizaci požadavek Azure Blob storage nebo Queue storage.

Seznam scénářů, pro které získávání tokenů se podporuje, najdete v článku [scénáře](https://aka.ms/msal-net-scenarios) část [Microsoft Authentication Library (MSAL) pro .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) úložiště GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: Vytvořit objekt blob bloku

Příklad kódu ukazuje, jak získat přístupový token ze služby Azure AD. Přístupový token se používá k ověřování zadaného uživatele a potom budete autorizovat požadavek na vytvoření objektu blob bloku. Pokud chcete získat to ukázka funguje, nejdřív postupujte podle kroků uvedených v předchozí části.

Chcete-li požádat o token, budete potřebovat následující hodnoty z registrace vaší aplikace:

- Název vaší domény Azure AD. Načíst tuto hodnotu ze zatížení **přehled** stránku služby Azure Active Directory.
- ID tenanta (nebo adresáře). Načíst tuto hodnotu ze zatížení **přehled** stránky registrace vaší aplikace.
- ID klienta (nebo aplikace). Načíst tuto hodnotu ze zatížení **přehled** stránky registrace vaší aplikace.
- Klient přesměrovací identifikátor URI. Načíst tuto hodnotu ze zatížení **ověřování** nastavení pro registraci vaší aplikace.
- Hodnota tajného kódu klienta. Načetlo tuto hodnotu z umístění, do které jste dříve zkopírovali ho.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Známé hodnoty pro ověřování pomocí Azure AD

K ověření objektu zabezpečení s využitím Azure AD, budete muset zahrnout některé známé hodnoty v kódu.

#### <a name="azure-ad-authority"></a>Autorita Azure AD

Pro veřejný cloud Microsoftu základní Azure AD autority vypadá takto, kde *id tenanta* je ID tenanta Active Directory (nebo ID adresáře):

`https://login.microsoftonline.com/<tenant-id>/`

ID tenanta identifikuje tenanta Azure AD pro účely ověření. To se také označuje jako ID adresáře. Pokud chcete načíst ID tenanta, přejděte na **přehled** stránce pro registraci vaší aplikace na webu Azure Portal a zkopírujte hodnotu z něj.

#### <a name="storage-resource-id"></a>ID prostředku úložiště

ID prostředku Azure Storage použijte k získání tokenu pro ověřování požadavků ve službě Azure Storage:

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Ke spuštění ukázky kódu, vytvořte účet úložiště ve stejném předplatném jako služba Azure Active Directory. Vytvořte kontejner v účtu úložiště. Vzorový kód vytvoří objekt blob bloku v tomto kontejneru.

V dalším kroku explicitně přiřadit **Přispěvatel dat objektu Blob úložiště** role má uživatelský účet, pod kterým bude spuštění vzorového kódu. Pokyny k přiřazení této role na webu Azure Portal najdete v tématu [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Při vytváření účtu služby Azure Storage, nejsou automaticky přiřadit oprávnění pro přístup k datům prostřednictvím Azure AD. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Ji můžete přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Vytvoření webové aplikace, který autorizuje přístup k úložišti objektů Blob v Azure AD

Pokud vaše aplikace přistupuje k Azure Storage, se podobně jménem uživatele, což znamená, že objekt blob nebo fronty prostředkům přistupuje pomocí oprávnění uživatele, který je přihlášen. Pokud chcete vyzkoušet tento příklad kódu, je nutné webovou aplikaci, která se zobrazí výzva k přihlášení pomocí identity Azure AD. Můžete vytvořit vlastní nebo použít ukázkové aplikace od Microsoftu.

Dokončené ukázkovou webovou aplikaci, která získá token a použije ho k vytvoření objektu blob ve službě Azure Storage je k dispozici na [Githubu](https://aka.ms/aadstorage). Prostudování a spuštění je hotová ukázka může být užitečné k pochopení příklady kódu. Pokyny ohledně toho, jak spustit úplnou vzorovou naleznete v části s názvem [zobrazení a spuštění je hotová ukázka](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Přidání odkazů a příkazy using  

Ze sady Visual Studio nainstalujte klientské knihovny Azure Storage. Z **nástroje** nabídce vyberte možnost **Správce balíčků Nuget**, pak **Konzola správce balíčků**. Zadejte následující příkazy do okna konzoly nainstalují potřebné balíčky v klientské knihovně Azure Storage pro .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

V dalším kroku přidejte následující příkazy using do souboru HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Vytvořit objekt blob bloku

Přidejte následující fragment kódu pro vytvoření objektu blob bloku:

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
> Povolit operace objektů blob a fronty k tokenu OAuth 2.0, musí používat protokol HTTPS.

V předchozím příkladu zpracovává klientské knihovny .NET autorizaci žádost o vytvoření objektů blob bloku. Klientské knihovny pro Azure Storage pro ostatní jazyky taky autorizaci žádosti za vás. Ale při volání operace služby Azure Storage k tokenu OAuth pomocí rozhraní REST API, pak bude nutné povolit tato žádost pomocí tokenu OAuth.

K volání operací služby objektů Blob a fronty použitím přístupových tokenů OAuth, předejte přístupového tokenu v **autorizace** pomocí hlavičky **nosiče** schéma a 2017-11-09 nebo vyšší jako verzi služby můžete vidět v následujícím příkladu:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Získání tokenu OAuth z Azure AD

V dalším kroku přidáte metodu, která žádá token ze služby Azure AD. Token, který požadujete bude jménem uživatele a budeme používat metodu GetTokenOnBehalfOfUser.

Pamatujte, že pokud jste nedávno přihlášení a jsou žádosti o token `storage.azure.com` prostředků, budete muset uživateli zprostředkovali uživatelského rozhraní, ve kterém může uživatel souhlas taková akce jejich jménem. Pro usnadnění, že je potřeba zachytit `MsalUiRequiredException` a přidávat funkce pro vyžádání souhlasu uživatele, jak je znázorněno v následujícím příkladu:

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
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Souhlas se proces udělení povolení k aplikaci přístup k chráněným prostředkům jejich jménem uživatele. Platforma identit Microsoft 2.0 podporuje přírůstkové souhlasu, to znamená, že objekt zabezpečení můžete nejdřív požádat o minimální sadu oprávnění a podle potřeby přidejte oprávnění v čase. Pokud váš kód požádá o přístupový token, určit obor oprávnění, která vaše aplikace potřebuje v daném okamžiku podle v `scope` parametru. Další informace o přírůstkové souhlasu, najdete v části s názvem **přírůstkové a dynamické souhlasu** v [Proč aktualizace pro Microsoft identity platform (v2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Následující metoda vytvoří vlastnosti ověřování pro požadování přírůstkové souhlasu:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
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

## <a name="view-and-run-the-completed-sample"></a>Zobrazovat a spouštět je hotová ukázka

Spuštění ukázkové aplikace, nejprve klonovat nebo stáhnout z [Githubu](https://aka.ms/aadstorage). Potom aktualizujte aplikaci, jak je popsáno v následujících částech.

### <a name="provide-values-in-the-settings-file"></a>Zadejte hodnoty v souboru nastavení

Dále, aktualizujte *appsettings.json* soubor pomocí vlastní hodnoty, následujícím způsobem:

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

### <a name="update-the-storage-account-and-container-name"></a>Aktualizace názvu účtu a kontejneru úložiště

V *HomeController.cs* souboru, aktualizujte identifikátor URI, který odkazuje na objekt blob bloku pro použití názvu účtu úložiště a kontejneru:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Povolit implicitní grant tok

Ke spuštění ukázky, budete muset nakonfigurovat implicitní grant tok pro registraci vaší aplikace. Postupujte následovně:

1. Přejděte do registrace vaší aplikace na webu Azure Portal.
1. V části Správa, vyberte **ověřování** nastavení.
1. V části **upřesňující nastavení**v **implicitní grant** vyberte zaškrtávací políčka Povolit přístupové tokeny a tokeny typu ID, jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky ukazující, jak povolit nastavení pro implicitní grant toku](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Aktualizujte port je používán localhost

Když spustíte ukázku, můžete zjistit, že je potřeba aktualizovat přesměrování identifikátor URI zadaný ve vaší registrace aplikace používat *localhost* portu přiřazené za běhu. Pokud chcete aktualizovat identifikátor URI pro použití portu přiřazené přesměrování, postupujte takto:

1. Přejděte do registrace vaší aplikace na webu Azure Portal.
1. V části Správa, vyberte **ověřování** nastavení.
1. V části **identifikátory URI přesměrování**, upravte port pro tak, aby odpovídaly, který se používá ukázková aplikace, jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky s identifikátory URI přesměrování pro registraci aplikace](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Další postup

- Další informace o platformě Microsoft identity, najdete v článku [platforma identit Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Další informace o rolích RBAC pro Azure storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Další informace o použití spravované identity pro prostředky Azure pomocí služby Azure Storage, najdete v článku [ověření přístupu k objektům BLOB a fronty s Azure Active Directory a spravovaným identitám pro prostředky Azure](storage-auth-aad-msi.md).
