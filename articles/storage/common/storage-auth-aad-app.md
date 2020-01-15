---
title: Získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace
titleSuffix: Azure Storage
description: Použijte Azure Active Directory k ověření v rámci klientské aplikace, získání tokenu OAuth 2,0 a autorizaci požadavků do úložiště objektů BLOB v Azure a do fronty.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ede43e3ed939083e7b5ff94899d12f6f4795a880
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941483"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace

Klíčovou výhodou použití Azure Active Directory (Azure AD) se službou Azure Blob Storage nebo Queue Storage je, že vaše přihlašovací údaje už nemusíte ukládat do kódu. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform (dřív Azure AD). Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, služba Azure AD vrátí přístupový token do aplikace a aplikace pak pomocí přístupového tokenu autorizuje žádosti do úložiště objektů BLOB v Azure nebo do úložiště fronty.

Tento článek ukazuje, jak nakonfigurovat nativní aplikaci nebo webovou aplikaci pro ověřování pomocí platformy Microsoft Identity Platform 2,0. Funkce .NET příklad kódu, ale jiných jazycích používat podobný přístup. Další informace o Microsoft Identity Platform 2,0 najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

Přehled toku přidělení kódu OAuth 2.0, naleznete v tématu [autorizovat přístup k Azure Active Directory webovým aplikacím pomocí OAuth 2.0 kódu udělit tok](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Přiřazení role k objektu zabezpečení služby Azure AD

K ověření objektu zabezpečení z vaší aplikace Azure Storage, nejprve nakonfigurujte nastavení řízení přístupu na základě rolí pro tento objekt zabezpečení. Azure Storage definuje předdefinované role RBAC, které zahrnují oprávnění pro kontejnery a fronty. Pokud RBAC role je přiřazena k objektu zabezpečení, tento objekt zabezpečení se udělí přístup k prostředku. Další informace najdete v tématu [Správa přístupových práv k objektům blob Azure a zařazování dat do fronty pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta služby Azure AD

Prvním krokem při použití Azure AD k autorizaci přístupu k prostředkům úložiště je registrace klientské aplikace pomocí klienta služby Azure AD z [Azure Portal](https://portal.azure.com). Při registraci klientské aplikace zadáváte informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID klienta (také nazývané *ID aplikace*), který používáte k aplikaci přidružit k Azure AD za běhu. Další informace o ID klienta najdete v tématu [aplikace a instanční objekty v Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Pokud chcete zaregistrovat Azure Storage aplikaci, postupujte podle kroků uvedených v části [rychlý Start: registrace aplikace s platformou Microsoft Identity](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Následující obrázek ukazuje běžné nastavení pro registraci webové aplikace:

![Snímek obrazovky, který ukazuje, jak zaregistrovat aplikaci úložiště ve službě Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Když si zaregistrujete aplikaci jako nativní aplikaci, můžete zadat libovolný platný identifikátor URI pro **identifikátor URI pro přesměrování**. Pro nativní aplikace nemusí být tato hodnota skutečnou adresou URL. Pro webové aplikace musí být identifikátor URI pro přesměrování platným identifikátorem URI, protože určuje adresu URL, na které jsou tokeny poskytovány.

Po zaregistrování vaší aplikace, zobrazí se vám ID aplikace (nebo ID klienta) v části **nastavení**:

![Snímek obrazovky zobrazující ID klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Registrované aplikaci udělit oprávnění ke službě Azure Storage

Potom udělte aplikaci oprávnění k volání rozhraní API Azure Storage. Tento krok umožňuje vaší aplikaci autorizovat žádosti o Azure Storage s využitím Azure AD.

1. Na stránce **Přehled** pro vaši registrovanou aplikaci vyberte **Zobrazit oprávnění rozhraní API**.
1. V části **oprávnění rozhraní API** vyberte **Přidat oprávnění** a zvolte **Microsoft API**.
1. V seznamu výsledků vyberte **Azure Storage** pro zobrazení PODOKNA **oprávnění API pro vyžádání** .
1. V části **jaký typ oprávnění vaše aplikace požaduje?** si všimněte, že dostupný typ oprávnění jsou **delegovaná oprávnění**. Tato možnost je ve výchozím nastavení vybrána.
1. V podokně **vybrat oprávnění** v PODOKNĚ **oprávnění API žádosti** zaškrtněte políčko vedle **User_impersonation**a pak klikněte na **Přidat oprávnění**.

    ![Snímek obrazovky zobrazující oprávnění pro úložiště](media/storage-auth-aad-app/registered-app-permissions-1.png)

V podokně **oprávnění API** se teď zobrazí, že registrovaná aplikace Azure AD má přístup k oběma Microsoft Graph i Azure Storage. Oprávnění se udělují Microsoft Graph automaticky při první registraci vaší aplikace ve službě Azure AD.

![Snímek obrazovky zobrazující oprávnění aplikace registrace](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Aplikace potřebuje při žádosti o tokenu klíč klienta k prokázání jeho identity. K přidání tajného klíče klienta použijte následující postup:

1. Přejděte k registraci vaší aplikace v Azure Portal.
1. Vyberte nastavení **certifikáty & tajných** kódů.
1. V části **tajné klíče klienta**klikněte na **nový tajný klíč klienta** a vytvořte nový tajný klíč.
1. Zadejte popis tajného kódu a vyberte požadovaný interval vypršení platnosti.
1. Hodnotu nového tajného klíče hned zkopírujte do zabezpečeného umístění. Celá hodnota se zobrazí pouze jednou.

    ![Snímek obrazovky zobrazující tajný klíč klienta](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získání tokenu

Po zaregistrování aplikace a udělení oprávnění k přístupu k datům v úložišti objektů BLOB v Azure nebo ve frontě můžete do své aplikace přidat kód pro ověření objektu zabezpečení a získání tokenu OAuth 2,0. K ověření a získání tokenu můžete použít buď jednu z [knihoven ověřování Microsoft Identity Platform](../../active-directory/develop/reference-v2-libraries.md) , nebo jinou Open Source knihovnu, která podporuje OpenID Connect 1,0. Aplikace pak může pomocí přístupového tokenu autorizovat požadavek na úložiště objektů BLOB v Azure nebo ve frontě.

Seznam scénářů, pro které se podporují tokeny, najdete v části [toky ověřování](/en-us/azure/active-directory/develop/msal-authentication-flows) [obsahu knihovny Microsoft Authentication Library](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Známé hodnoty pro ověřování pomocí Azure AD

K ověření objektu zabezpečení s využitím Azure AD, budete muset zahrnout některé známé hodnoty v kódu.

### <a name="azure-ad-authority"></a>Autorita Azure AD

Pro veřejný cloud Microsoftu základní Azure AD autority vypadá takto, kde *id tenanta* je ID tenanta Active Directory (nebo ID adresáře):

`https://login.microsoftonline.com/<tenant-id>/`

ID tenanta identifikuje tenanta Azure AD pro účely ověření. Také se označuje jako ID adresáře. Pokud chcete načíst ID tenanta, přejděte na stránku **Přehled** pro registraci vaší aplikace v Azure Portal a zkopírujte hodnotu z tohoto umístění.

### <a name="azure-storage-resource-id"></a>ID prostředku Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: vytvoření objektu blob bloku

Příklad kódu ukazuje, jak získat přístupový token ze služby Azure AD. Přístupový token se používá k ověřování zadaného uživatele a potom budete autorizovat požadavek na vytvoření objektu blob bloku. Pokud chcete získat to ukázka funguje, nejdřív postupujte podle kroků uvedených v předchozí části.

K vyžádání tokenu budete potřebovat následující hodnoty z registrace vaší aplikace:

- Název vaší domény služby Azure AD. Tuto hodnotu si načtěte na stránce **přehled** Azure Active Directory.
- ID tenanta (nebo adresáře). Tuto hodnotu si načtěte na stránce **Přehled** registrace vaší aplikace.
- ID klienta (nebo aplikace). Tuto hodnotu si načtěte na stránce **Přehled** registrace vaší aplikace.
- Identifikátor URI pro přesměrování klienta. Načtěte tuto hodnotu z nastavení **ověřování** pro registraci vaší aplikace.
- Hodnota tajného klíče klienta. Načte tuto hodnotu z umístění, do kterého jste ji dříve zkopírovali.

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Chcete-li spustit ukázku kódu, vytvořte účet úložiště v rámci stejného předplatného jako váš Azure Active Directory. Pak vytvořte kontejner v rámci tohoto účtu úložiště. Vzorový kód vytvoří objekt blob bloku v tomto kontejneru.

Dále explicitně přiřaďte roli **Přispěvatel dat objektů BLOB úložiště** k uživatelskému účtu, pod kterým budete spouštět vzorový kód. Pokyny k přiřazení této role v Azure Portal najdete v tématu [udělení přístupu k datům objektů blob Azure a frontě pomocí RBAC v Azure Portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Když vytvoříte účet Azure Storage, nebudete automaticky přiřazovat oprávnění pro přístup k datům prostřednictvím služby Azure AD. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Ji můžete přiřadit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Vytvoření webové aplikace, která autorizuje přístup k úložišti objektů BLOB pomocí Azure AD

Když vaše aplikace přistupuje k Azure Storage, provede její jménem uživatele, což znamená, že se k prostředkům BLOB nebo Queue přistupuje pomocí oprávnění přihlášeného uživatele. Chcete-li vyzkoušet tento příklad kódu, potřebujete webovou aplikaci, která vyzve uživatele k přihlášení pomocí identity Azure AD. Můžete vytvořit vlastní nebo použít ukázkovou aplikaci poskytovanou Microsoftem.

Hotová Ukázková webová aplikace, která získá token a používá ho k vytvoření objektu BLOB v Azure Storage je k dispozici na [GitHubu](https://aka.ms/aadstorage). Kontrola a spuštění hotové ukázky může být užitečné pro porozumění příkladům kódu. Pokyny pro spuštění dokončené ukázky najdete v části s názvem [zobrazení a spuštěním dokončené ukázky](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Přidání odkazů a příkazy using  

V aplikaci Visual Studio nainstalujte Azure Storage klientské knihovny. V nabídce **Nástroje** vyberte **Správce balíčků NuGet** a potom **Konzola Správce balíčků**. Do okna konzoly zadejte následující příkazy pro instalaci potřebných balíčků z klientské knihovny Azure Storage pro .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Dále do souboru HomeController.cs přidejte následující příkazy using:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Vytvoření objektu blob bloku

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
> K autorizaci operací objektů BLOB a Queue s tokenem OAuth 2,0 je nutné použít protokol HTTPS.

V předchozím příkladu zpracovává klientské knihovny .NET autorizaci žádost o vytvoření objektů blob bloku. Azure Storage klientské knihovny pro jiné jazyky také pořídí autorizaci žádosti za vás. Ale při volání operace služby Azure Storage k tokenu OAuth pomocí rozhraní REST API, pak bude nutné povolit tato žádost pomocí tokenu OAuth.

K volání operací služby objektů Blob a fronty použitím přístupových tokenů OAuth, předejte přístupového tokenu v **autorizace** pomocí hlavičky **nosiče** schéma a 2017-11-09 nebo vyšší jako verzi služby můžete vidět v následujícím příkladu:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Získání tokenu OAuth z Azure AD

Dále přidejte metodu, která vyžaduje token od Azure AD jménem uživatele. Tato metoda definuje obor, pro který mají být udělena oprávnění. Další informace o oprávněních a oborech najdete [v tématu oprávnění a souhlas v koncovém bodě Microsoft Identity Platform](../../active-directory/develop/v2-permissions-and-consent.md).

K vytvoření oboru, pro který se má získat token, použijte ID prostředku. Tento příklad sestaví obor pomocí ID prostředku společně s integrovaným `user_impersonation`m oborem, který indikuje, že je token požadován jménem uživatele.

Mějte na paměti, že možná budete muset uživatele prezentovat pomocí rozhraní, které uživateli umožňuje udělit souhlas s žádostí o jeho jménem. Když je souhlas nutný, příklad zachytí **MsalUiRequiredException** a zavolá jinou metodu pro usnadnění žádosti o souhlas:

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

Souhlas je proces uživatele, který uděluje oprávnění k přístupu k chráněným prostředkům jménem uživatele. Microsoft Identity Platform 2,0 podporuje přírůstkový souhlas, což znamená, že instanční objekt může nejdřív požádat o minimální sadu oprávnění a přidat oprávnění v průběhu času podle potřeby. Když váš kód požádá o přístupový token, určete rozsah oprávnění, která vaše aplikace v daném okamžiku potřebuje, v parametru `scope`. Další informace o přírůstkovém souhlasu najdete v části s názvem **přírůstkové a dynamické vyjádření souhlasu** v tématu [aktualizace platformy Microsoft Identity Platform (v 2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Následující metoda vytvoří vlastnosti ověřování pro vyžadování přírůstkového souhlasu:

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

Pokud chcete ukázkovou aplikaci spustit, nejdřív ji naklonujte nebo stáhněte z [GitHubu](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Pak aplikaci aktualizujte, jak je popsáno v následujících částech.

### <a name="provide-values-in-the-settings-file"></a>Zadejte hodnoty do souboru nastavení.

Dále aktualizujte soubor *appSettings. JSON* vlastními hodnotami následujícím způsobem:

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

V souboru *HomeController.cs* aktualizujte identifikátor URI, který odkazuje na objekt blob bloku, aby používal název vašeho účtu a kontejneru úložiště:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Povolit tok implicitního udělení

Pokud chcete ukázku spustit, možná budete muset nakonfigurovat tok implicitního udělení pro registraci vaší aplikace. Postupujte následovně:

1. Přejděte k registraci vaší aplikace v Azure Portal.
1. V části Spravovat vyberte nastavení **ověřování** .
1. V části **Upřesnit nastavení**v části **implicitní přístup** zaškrtněte políčka pro povolení přístupových tokenů a tokenů ID, jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky ukazující, jak povolit nastavení pro implicitní tok udělení](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Aktualizace portu používaného localhost

Když ukázku spustíte, můžete zjistit, že je potřeba aktualizovat identifikátor URI přesměrování zadaný v registraci aplikace tak, aby používal port *localhost* přiřazený za běhu. Chcete-li aktualizovat identifikátor URI pro přesměrování tak, aby používal přiřazený port, postupujte podle následujících kroků:

1. Přejděte k registraci vaší aplikace v Azure Portal.
1. V části Spravovat vyberte nastavení **ověřování** .
1. V části **identifikátory URI pro přesměrování**upravte port tak, aby odpovídal formátu používanému ukázkovou aplikací, jak je znázorněno na následujícím obrázku:

    ![Snímek obrazovky ukazující identifikátory URI přesměrování pro registraci aplikace](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Další kroky

- Další informace o platformě Microsoft identity najdete v tématu [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/).
- Další informace o rolích RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Další informace o použití spravovaných identit pro prostředky Azure s Azure Storage najdete v tématu [ověřování přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md).
