---
title: Získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace
titleSuffix: Azure Storage
description: Použijte Azure Active Directory k ověření v rámci klientské aplikace, získání tokenu OAuth 2,0 a autorizaci požadavků do úložiště objektů BLOB v Azure a do fronty.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: a0ce2c17586e5437047ff27cb67577b0480a83af
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939337"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace

Klíčovou výhodou použití Azure Active Directory (Azure AD) se službou Azure Blob Storage nebo Queue Storage je, že vaše přihlašovací údaje už nemusíte ukládat do kódu. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform. Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, služba Azure AD vrátí přístupový token do aplikace a aplikace pak pomocí přístupového tokenu autorizuje žádosti do úložiště objektů BLOB v Azure nebo do úložiště fronty.

Tento článek ukazuje, jak nakonfigurovat nativní aplikaci nebo webovou aplikaci pro ověřování pomocí platformy Microsoft identity pomocí ukázkové aplikace, která je k dispozici ke stažení. Ukázková funkce aplikace .NET, ale jiné jazyky používají podobný přístup. Další informace o platformě Microsoft identity najdete v tématu [Přehled platformy Microsoft Identity](../../active-directory/develop/v2-overview.md).

Přehled toku udělení kódu OAuth 2,0 najdete v tématu [autorizace přístupu k Azure Active Directory webových aplikací pomocí toku udělení kódu oauth 2,0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>O ukázkové aplikaci

Ukázková aplikace nabízí ucelené prostředí, které ukazuje, jak nakonfigurovat webovou aplikaci pro ověřování pomocí služby Azure AD v místním vývojovém prostředí. Pokud chcete zobrazit a spustit ukázkovou aplikaci, nejdřív ji naklonujte nebo si stáhněte z [GitHubu](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Pak postupujte podle kroků uvedených v článku Konfigurace registrace aplikace Azure a aktualizace aplikace pro vaše prostředí.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Přiřazení role k objektu zabezpečení služby Azure AD

Pokud chcete ověřit objekt zabezpečení z vaší aplikace Azure Storage, nejdřív nakonfigurujte nastavení řízení přístupu na základě role Azure (Azure RBAC) pro daný objekt zabezpečení. Azure Storage definuje předdefinované role, které zahrnují oprávnění pro kontejnery a fronty. Když je role Azure přiřazená k objektu zabezpečení, je tomuto objektu zabezpečení udělen přístup k tomuto prostředku. Další informace najdete v tématu [Správa přístupových práv k objektům blob Azure a zařazování dat do front pomocí Azure RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta Azure AD

Prvním krokem při použití Azure AD k autorizaci přístupu k prostředkům úložiště je registrace klientské aplikace pomocí klienta služby Azure AD z [Azure Portal](https://portal.azure.com). Při registraci klientské aplikace zadáváte informace o aplikaci do služby Azure AD. Azure AD pak poskytuje ID klienta (označované také jako *ID aplikace*), které můžete použít k přidružení aplikace k Azure AD za běhu. Další informace o ID klienta najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). Pokud chcete zaregistrovat Azure Storage aplikaci, postupujte podle kroků uvedených v části [rychlý Start: registrace aplikace s platformou Microsoft Identity](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). 

Následující obrázek ukazuje společná nastavení pro registraci webové aplikace. Všimněte si, že v tomto příkladu je identifikátor URI přesměrování nastaven na `http://localhost:5000/signin-oidc` pro testování ukázkové aplikace ve vývojovém prostředí. Toto nastavení můžete později změnit pod nastavením **ověřování** pro registrovanou aplikaci v Azure Portal:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Snímek obrazovky, který ukazuje, jak zaregistrovat aplikaci úložiště ve službě Azure AD":::

> [!NOTE]
> Pokud aplikaci zaregistrujete jako nativní aplikaci, můžete pro **identifikátor URI přesměrování**zadat libovolný platný identifikátor URI. Pro nativní aplikace nemusí být tato hodnota skutečnou adresou URL. Pro webové aplikace musí být identifikátor URI pro přesměrování platným identifikátorem URI, protože určuje adresu URL, na které jsou tokeny poskytovány.

Po zaregistrování aplikace se v části **Nastavení**zobrazí ID aplikace (nebo ID klienta):

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Snímek obrazovky, který ukazuje, jak zaregistrovat aplikaci úložiště ve službě Azure AD":::

Další informace o registraci aplikace v Azure AD najdete v tématu [integrování aplikací pomocí Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Udělte registrovaným oprávněním aplikace Azure Storage

Potom udělte aplikaci oprávnění k volání rozhraní API Azure Storage. Tento krok umožňuje vaší aplikaci autorizovat žádosti o Azure Storage s využitím Azure AD.

1. Na stránce **oprávnění rozhraní API** pro vaši registrovanou aplikaci vyberte **Přidat oprávnění**.
1. Na kartě **rozhraní Microsoft API** vyberte **Azure Storage**.
1. V podokně **oprávnění API pro žádosti** v části **jaký typ oprávnění vaše aplikace požaduje? zkontrolujte**, jestli je dostupný typ oprávnění **delegovaná oprávnění**. Tato možnost je ve výchozím nastavení vybrána.
1. V části **oprávnění**zaškrtněte políčko vedle **user_impersonation**a pak vyberte tlačítko **Přidat oprávnění** .

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Snímek obrazovky, který ukazuje, jak zaregistrovat aplikaci úložiště ve službě Azure AD":::

1. Potom pro tato oprávnění udělte souhlas správce kliknutím na **udělit souhlas správce pro výchozí adresář**.

V podokně **oprávnění rozhraní API** se teď zobrazí, že vaše registrovaná aplikace Azure AD má přístup k rozhraním API Microsoft Graph i Azure Storage a že je pro výchozí adresář udělený souhlas. Oprávnění se udělují Microsoft Graph automaticky při první registraci vaší aplikace ve službě Azure AD.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Snímek obrazovky, který ukazuje, jak zaregistrovat aplikaci úložiště ve službě Azure AD":::

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Aplikace potřebuje při žádosti o tokenu klíč klienta k prokázání jeho identity. K přidání tajného klíče klienta použijte následující postup:

1. Přejděte k registraci vaší aplikace v Azure Portal.
1. Vyberte nastavení **certifikáty & tajných** kódů.
1. V části **tajné klíče klienta**klikněte na **nový tajný klíč klienta** a vytvořte nový tajný klíč.
1. Zadejte popis tajného kódu a vyberte požadovaný interval vypršení platnosti.
1. Hodnotu nového tajného klíče hned zkopírujte do zabezpečeného umístění. Celá hodnota se zobrazí pouze jednou.

    ![Snímek obrazovky zobrazující tajný klíč klienta](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Povolit tok implicitního udělení

Dále nakonfigurujte pro aplikaci implicitní tok udělení. Postupujte takto:

1. Přejděte k registraci vaší aplikace v Azure Portal.
1. V části **Spravovat** vyberte nastavení **ověřování** .
1. V části **implicitní grant** zaškrtněte políčko, pokud chcete povolit tokeny ID, jak je znázorněno na následujícím obrázku:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Snímek obrazovky, který ukazuje, jak zaregistrovat aplikaci úložiště ve službě Azure AD":::

## <a name="client-libraries-for-token-acquisition"></a>Klientské knihovny pro získání tokenu

Po zaregistrování aplikace a udělení oprávnění k přístupu k datům v úložišti objektů BLOB v Azure nebo ve frontě můžete do své aplikace přidat kód pro ověření objektu zabezpečení a získání tokenu OAuth 2,0. K ověření a získání tokenu můžete použít buď jednu z [knihoven ověřování Microsoft Identity Platform](../../active-directory/develop/reference-v2-libraries.md) , nebo jinou Open Source knihovnu, která podporuje OpenID Connect 1,0. Aplikace pak může pomocí přístupového tokenu autorizovat požadavek na úložiště objektů BLOB v Azure nebo ve frontě.

Seznam scénářů, pro které se podporují tokeny, najdete v části [toky ověřování](/en-us/azure/active-directory/develop/msal-authentication-flows) v dokumentaci k [Microsoft Authentication Library (MSAL)](/azure/active-directory/develop/msal-overview) .

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Známé hodnoty pro ověřování pomocí Azure AD

K ověření objektu zabezpečení ve službě Azure AD je nutné zahrnout do kódu některé známé hodnoty.

### <a name="azure-ad-authority"></a>Autorita Azure AD

Základní autorita Azure AD pro veřejný cloud Microsoftu je následující, kde *ID tenanta* je vaše ID tenanta služby Active Directory (nebo ID adresáře):

`https://login.microsoftonline.com/<tenant-id>/`

ID tenanta identifikuje tenanta Azure AD, který se má použít pro ověřování. Také se označuje jako ID adresáře. Pokud chcete načíst ID tenanta, přejděte na stránku **Přehled** pro registraci vaší aplikace v Azure Portal a zkopírujte hodnotu z tohoto umístění.

### <a name="azure-storage-resource-id"></a>ID prostředku Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Příklad kódu .NET: vytvoření objektu blob bloku

Příklad kódu ukazuje, jak získat přístupový token ze služby Azure AD. Přístupový token se používá k ověření zadaného uživatele a následnému autorizaci žádosti o vytvoření objektu blob bloku. Chcete-li získat tuto ukázkovou práci, postupujte nejdříve podle kroků uvedených v předchozích částech.

K vyžádání tokenu budete potřebovat následující hodnoty z registrace vaší aplikace:

- Název vaší domény služby Azure AD. Tuto hodnotu si načtěte na stránce **přehled** Azure Active Directory.
- ID tenanta (nebo adresáře). Tuto hodnotu si načtěte na stránce **Přehled** registrace vaší aplikace.
- ID klienta (nebo aplikace). Tuto hodnotu si načtěte na stránce **Přehled** registrace vaší aplikace.
- Identifikátor URI pro přesměrování klienta. Načtěte tuto hodnotu z nastavení **ověřování** pro registraci vaší aplikace.
- Hodnota tajného klíče klienta. Načte tuto hodnotu z umístění, do kterého jste ji dříve zkopírovali.

### <a name="create-a-storage-account-and-container"></a>Vytvoření účtu úložiště a kontejneru

Chcete-li spustit ukázku kódu, vytvořte účet úložiště v rámci stejného předplatného jako váš Azure Active Directory. Pak vytvořte kontejner v rámci tohoto účtu úložiště. Vzorový kód vytvoří objekt blob bloku v tomto kontejneru.

Dále explicitně přiřaďte roli **Přispěvatel dat objektů BLOB úložiště** k uživatelskému účtu, pod kterým budete spouštět vzorový kód. Pokyny k přiřazení této role v Azure Portal najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Když vytvoříte účet Azure Storage, nebudete automaticky přiřazovat oprávnění pro přístup k datům prostřednictvím služby Azure AD. Musíte explicitně přiřadit roli Azure pro Azure Storage. Můžete ji přiřadit na úrovni předplatného, skupiny prostředků, účtu úložiště nebo kontejneru nebo fronty.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Vytvoření webové aplikace, která autorizuje přístup k úložišti objektů BLOB pomocí Azure AD

Když vaše aplikace přistupuje k Azure Storage, provede její jménem uživatele, což znamená, že se k prostředkům BLOB nebo Queue přistupuje pomocí oprávnění přihlášeného uživatele. Chcete-li vyzkoušet tento příklad kódu, potřebujete webovou aplikaci, která vyzve uživatele k přihlášení pomocí identity Azure AD. Můžete vytvořit vlastní nebo použít ukázkovou aplikaci poskytovanou Microsoftem.

Hotová Ukázková webová aplikace, která získá token a používá ho k vytvoření objektu BLOB v Azure Storage je k dispozici na [GitHubu](https://aka.ms/aadstorage). Kontrola a spuštění hotové ukázky může být užitečné pro porozumění příkladům kódu. Pokyny pro spuštění dokončené ukázky najdete v části s názvem [zobrazení a spuštěním dokončené ukázky](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Přidání odkazů a použití příkazů  

V aplikaci Visual Studio nainstalujte Azure Storage klientské knihovny. V nabídce **Nástroje** vyberte **Správce balíčků NuGet** a potom **Konzola Správce balíčků**. Do okna konzoly zadejte následující příkazy pro instalaci potřebných balíčků z klientské knihovny Azure Storage pro .NET:

# <a name="net-v12-sdk"></a>[Sada .NET V12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Dále do souboru HomeController.cs přidejte následující příkazy using:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[Sada .NET V11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Dále do souboru HomeController.cs přidejte následující příkazy using:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Vytvoření objektu blob bloku

Přidejte následující fragment kódu pro vytvoření objektu blob bloku. Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

# <a name="net-v12-sdk"></a>[Sada .NET V12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[Sada .NET V11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> K autorizaci operací objektů BLOB a Queue s tokenem OAuth 2,0 je nutné použít protokol HTTPS.

V předchozím příkladu knihovna klienta .NET zpracovává autorizaci žádosti o vytvoření objektu blob bloku. Azure Storage klientské knihovny pro jiné jazyky také pořídí autorizaci žádosti za vás. Pokud ale voláte operaci Azure Storage s tokenem OAuth pomocí REST API, pak budete muset vytvořit **autorizační** hlavičku pomocí tokenu OAuth.

Pokud chcete volat operace BLOB a Služba front pomocí přístupových tokenů OAuth, předejte přístupový token v **autorizační** hlavičce pomocí **nosného** schématu a zadejte verzi služby 2017-11-09 nebo vyšší, jak je znázorněno v následujícím příkladu:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Získání přístupového tokenu z Azure AD

Dále přidejte metodu, která vyžaduje token od Azure AD jménem uživatele. Tato metoda definuje obor, pro který mají být udělena oprávnění. Další informace o oprávněních a oborech najdete [v tématu oprávnění a souhlas v koncovém bodě Microsoft Identity Platform](../../active-directory/develop/v2-permissions-and-consent.md).

K vytvoření oboru, pro který se má získat token, použijte ID prostředku. Tento příklad vytvoří obor pomocí ID prostředku společně s předdefinovaným `user_impersonation` rozsahem, který označuje, že je token požadován jménem uživatele.

Mějte na paměti, že možná budete muset uživatele prezentovat pomocí rozhraní, které uživateli umožňuje udělit souhlas s žádostí o jeho jménem:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Souhlas je proces uživatele, který uděluje oprávnění k přístupu k chráněným prostředkům jménem uživatele. Platforma Microsoft Identity Platform podporuje přírůstkový souhlas, což znamená, že instanční objekt může nejdřív vyžádat minimální sadu oprávnění a v průběhu času přidat oprávnění podle potřeby. Když kód požádá o přístupový token, určete rozsah oprávnění, která vaše aplikace potřebuje. Další informace o přírůstkovém souhlasu najdete v tématu [přírůstkové a dynamické vyjádření souhlasu](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>Zobrazení a spuštění dokončené ukázky

Pokud chcete ukázkovou aplikaci spustit, nejdřív ji naklonujte nebo stáhněte z [GitHubu](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Pak aplikaci aktualizujte, jak je popsáno v následujících částech.

### <a name="provide-values-in-the-settings-file"></a>Zadejte hodnoty do souboru nastavení.

Aktualizujte *appsettings.jsv* souboru vlastními hodnotami následujícím způsobem:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>"
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Aktualizace účtu úložiště a názvu kontejneru

V souboru *HomeController.cs* aktualizujte identifikátor URI, který odkazuje na objekt blob bloku, aby používal název vašeho účtu a kontejneru úložiště, a nahradí hodnoty v lomených závorkách vlastními hodnotami:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Další kroky

- [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/)
- [Správa přístupových práv k datům úložiště pomocí Azure RBAC](storage-auth-aad-rbac.md)
- [Ověřování přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md)
