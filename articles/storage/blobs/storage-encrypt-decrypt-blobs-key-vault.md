---
title: Kurz – šifrování a dešifrování objektů BLOB pomocí Azure Key Vault
titleSuffix: Azure Storage
description: Naučte se šifrovat a dešifrovat objekt BLOB pomocí šifrování na straně klienta pomocí Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: c2daed4a8df89ed176749900dc75eb231c00af87
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049266"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Kurz – šifrování a dešifrování objektů BLOB pomocí Azure Key Vault

Tento kurz se věnuje tomu, jak používat šifrování úložiště na straně klienta s Azure Key Vault. Provede vás postupy šifrování a dešifrování objektu BLOB v konzolové aplikaci pomocí těchto technologií.

**Odhadovaný čas dokončení:** 20 minut

Přehled informací o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md).

Přehled informací o šifrování na straně klienta pro Azure Storage najdete v tématu [šifrování na straně klienta a Azure Key Vault Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Účet Azure Storage
* Visual Studio 2013 nebo novější
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Přehled šifrování na straně klienta

Přehled šifrování na straně klienta pro Azure Storage najdete v tématu šifrování na [straně klienta a Azure Key Vault pro Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Tady je stručný popis toho, jak funguje šifrování na straně klienta:

1. Sada SDK Azure Storage klienta generuje šifrovací klíč obsahu (CEK), což je symetrický klíč založený na jednorázovém použití.
2. Zákaznická data se šifrují pomocí tohoto CEK.
3. CEK se pak zabalí (zašifruje) pomocí klíčového šifrovacího klíče (KEK). KEK je identifikován identifikátorem klíče a může se jednat o asymetrický klíč nebo symetrický klíč a lze ho spravovat místně nebo uložit v Azure Key Vault. Klient úložiště nemá nikdy přístup k KEK. Pouze vyvolá algoritmus zalamování klíčů, který je k dispozici v Key Vault. Zákazníci si můžou vybrat, jestli chtějí používat vlastní poskytovatele pro zabalení a rozbalení klíče, pokud chtějí.
4. Šifrovaná data se pak nahrají do služby Azure Storage.

## <a name="set-up-your-azure-key-vault"></a>Nastavení Azure Key Vault

Aby bylo možné pokračovat v tomto kurzu, je třeba provést následující kroky, které jsou popsanými v kurzu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí webové aplikace .NET](../../key-vault/secrets/quick-create-net.md):

* Vytvořte trezor klíčů.
* Přidejte klíč nebo tajný klíč do trezoru klíčů.
* Registrace aplikace pomocí Azure Active Directory.
* Autorizujte aplikaci pro použití klíče nebo tajného klíče.

Poznamenejte si ClientID a ClientSecret, které byly generovány při registraci aplikace pomocí Azure Active Directory.

V trezoru klíčů vytvořte oba klíče. Předpokládáme, že zbytek kurzu používá následující názvy: ContosoKeyVault a TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Vytvoření konzolové aplikace s balíčky a AppSettings

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci.

Přidejte potřebné balíčky NuGet do konzoly Správce balíčků.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Přidat AppSettings do App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Přidejte následující `using` direktivy a nezapomeňte přidat odkaz na System.Configuration do projektu.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```
---

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Přidejte metodu pro získání tokenu do konzolové aplikace.

Následující metodu používá Key Vault třídy, které se musí ověřit pro přístup k trezoru klíčů.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```
---

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Přístup k Azure Storage a Key Vault v programu

V metodě Main () přidejte následující kód.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```
---

> [!NOTE]
> Modely Key Vault objektů
> 
> Je důležité si uvědomit, že ve skutečnosti existují dva Key Vault objektové modely, o kterých byste měli vědět: jedna je založená na REST API (obor názvů trezoru klíčů) a druhá je rozšířením pro šifrování na straně klienta.
> 
> Klient Key Vault komunikuje s REST API a porozumět webovým klíčům a tajným klíčům JSON pro dva druhy věcí, které jsou obsaženy v Key Vault.
> 
> Rozšíření Key Vault jsou třídy, které se v Azure Storage jeví jako speciálně vytvořené pro šifrování na straně klienta. Obsahují rozhraní pro klíče (IKey) a třídy založené na konceptu překladače klíčů. Existují dvě implementace IKey, které potřebujete znát: RSAKey a SymmetricKey. Nyní se tyto věci shodují s akcemi, které jsou obsaženy v Key Vault, ale v tomto okamžiku jsou nezávislé třídy (takže klíč a tajný kód načtený klientem Key Vault neimplementuje IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Šifrování a nahrání objektů BLOB

Přidejte následující kód k zašifrování objektu BLOB a nahrajte ho do svého účtu úložiště Azure. Použitá metoda **ResolveKeyAsync** vrací IKey.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```
---

> [!NOTE]
> Pokud se podíváte na konstruktor BlobEncryptionPolicy, uvidíte, že může přijmout klíč nebo překladač. Mějte na paměti, že teď nemůžete použít překladač pro šifrování, protože v současné době nepodporuje výchozí klíč.


## <a name="decrypt-blob-and-download"></a>Dešifrovat objekt BLOB a stáhnout

Dešifrování je ve skutečnosti vhodné při použití tříd překladače. ID klíče používaného pro šifrování je přidruženo k objektu BLOB ve svých metadatech, takže neexistuje žádný důvod, proč byste klíč načetli a zapamatovali přidružení mezi klíčem a objektem BLOB. Stačí, abyste se ujistili, že klíč zůstane v Key Vault.   

Privátní klíč klíče RSA zůstává v Key Vault, takže pokud k tomu dojde, zašifrované klíče z metadat objektů blob, které obsahují CEK, se odešlou Key Vault k dešifrování.

K dešifrování objektu blob, který jste právě Nahráli, přidejte následující.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```
---

> [!NOTE]
> Existuje několik dalších druhů překladačů, které usnadňují správu klíčů, mezi které patří: AggregateKeyResolver a CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Použití Key Vault tajných klíčů

Způsob použití tajného klíče se šifrováním na straně klienta je prostřednictvím třídy SymmetricKey, protože tajný klíč je v podstatě symetrický klíč. Jak je uvedeno výše, tajný kód v Key Vault není mapován přesně na SymmetricKey. Je potřeba pochopit několik věcí:

* Klíč v SymmetricKey musí být pevně daná Délka: 128, 192, 256, 384 nebo 512 bitů.
* Klíč v SymmetricKey by měl být kódovaný v kódování Base64.
* Key Vault tajný klíč, který se použije jako SymmetricKey, musí mít v Key Vault typ obsahu "Application/oktet-Stream".

Tady je příklad v PowerShellu vytvoření tajného klíče v Key Vault, který se dá použít jako SymmetricKey.
Upozorňujeme, že pevně kódovaná hodnota, $key, slouží pouze k demonstračnímu účelu. Ve svém vlastním kódu budete chtít vygenerovat tento klíč.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

V konzolové aplikaci můžete použít stejné volání jako předtím k načtení tohoto tajného klíče jako SymmetricKey.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
---

## <a name="next-steps"></a>Další kroky

Další informace o použití Microsoft Azure Storage v jazyce C# najdete v tématu [Microsoft Azure Storage Klientská knihovna pro .NET](/previous-versions/azure/dn261237(v=azure.100)).

Další informace o REST API objektů BLOB najdete v tématu [REST API služby BLOB Service](/rest/api/storageservices/Blob-Service-REST-API).

Nejnovější informace o Microsoft Azure Storage najdete na [blogu Microsoft Azure Storage týmu](/archive/blogs/windowsazurestorage/).