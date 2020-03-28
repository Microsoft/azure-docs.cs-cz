---
title: Výuka – šifrování a dešifrování objektů BLOB pomocí služby Azure Key Vault
titleSuffix: Azure Storage
description: Zjistěte, jak šifrovat a dešifrovat objekt blob pomocí šifrování na straně klienta pomocí azure key vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c83e56a47f4b212a5612cb9e6965ce8e73228dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74892885"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Výuka – šifrování a dešifrování objektů BLOB pomocí služby Azure Key Vault

Tento kurz popisuje, jak využít šifrování úložiště na straně klienta s Azure Key Vault. Provede vás, jak šifrovat a dešifrovat objekt blob v konzolové aplikaci pomocí těchto technologií.

**Odhadovaný čas dokončení:** 20 minut

Informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md).

Přehled informací o šifrování na straně klienta pro Azure Storage najdete v [tématu Šifrování na straně klienta a Trezor klíčů Azure pro úložiště Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Účet úložiště Azure
* Visual Studio 2013 nebo novější
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Přehled šifrování na straně klienta

Přehled šifrování na straně klienta pro Azure Storage najdete v [tématu Šifrování na straně klienta a Azure Key Vault pro úložiště Microsoft Azure.](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Zde je stručný popis toho, jak funguje šifrování na straně klienta:

1. Sada Azure Storage klienta generuje šifrovací klíč obsahu (CEK), což je jednorázový symetrický klíč.
2. Zákaznická data jsou šifrována pomocí této CEK.
3. CEK je pak zabalen (šifrován) pomocí šifrovacího klíče (KEK). KEK je identifikován identifikátor klíče a může být asymetrický pár klíčů nebo symetrický klíč a lze spravovat místně nebo uloženy v trezoru klíčů Azure. Samotný klient úložiště nikdy nemá přístup k KEK. Pouze vyvolá algoritmus obtékání klíčů, který je k dispozici trezoru klíčů. Zákazníci si mohou vybrat, zda budou používat vlastní zprostředkovatele pro obtékání klíčů nebo rozbalování, pokud chtějí.
4. Šifrovaná data se pak nahrají do služby Azure Storage.

## <a name="set-up-your-azure-key-vault"></a>Nastavení trezoru klíčů Azure

Chcete-li pokračovat v tomto kurzu, musíte provést následující kroky, které jsou popsány v kurzu [Úvodní příručka: Nastavení a načtení tajného klíče z trezoru klíčů Azure pomocí webové aplikace .NET](../../key-vault/quick-create-net.md):

* Vytvoření trezoru klíčů
* Přidejte klíč nebo tajný klíč do trezoru klíčů.
* Zaregistrujte aplikaci pomocí služby Azure Active Directory.
* Autorizujte aplikaci k použití klíče nebo tajného klíče.

Poznamenejte si ClientID a ClientSecret, které byly generovány při registraci aplikace s Azure Active Directory.

Vytvořte obě klíče v trezoru klíčů. Předpokládáme, že pro zbytek kurzu, který jste použili následující názvy: ContosoKeyVault a TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Vytvoření konzolové aplikace s balíčky a nastavením aplikací

V sadě Visual Studio vytvořte novou konzolovou aplikaci.

Přidejte potřebné balíčky nuget u konzole Správce balíčků.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Přidejte AppSettings do App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Přidejte `using` následující direktivy a nezapomeňte do projektu přidat odkaz na System.Configuration.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Přidání metody pro získání tokenu do konzolové aplikace

Následující metoda je použita key vault třídy, které je třeba ověřit pro přístup k trezoru klíčů.

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

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Přístup k úložišti Azure a trezoru klíčů ve vašem programu

V Main() metoda, přidejte následující kód.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey");
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modely objektů trezoru klíčů
> 
> Je důležité si uvědomit, že ve skutečnosti existují dva modely objektů Key Vault, které je třeba znát: jeden je založen na rozhraní REST API (KeyVault namespace) a druhý je rozšíření pro šifrování na straně klienta.
> 
> Klient trezoru klíčů spolupracuje s rozhraním REST API a rozumí webovým klíčům JSON a tajným kódům pro dva druhy věcí, které jsou obsaženy v trezoru klíčů.
> 
> Rozšíření trezoru klíčů jsou třídy, které se zdají být speciálně vytvořené pro šifrování na straně klienta ve službě Azure Storage. Obsahují rozhraní pro klíče (IKey) a třídy založené na konceptu překládání klíčů. Existují dvě implementace IKey, které potřebujete vědět: RSAKey a SymmetricKey. Nyní se náhodou shodují s věcmi, které jsou obsaženy v trezoru klíčů, ale v tomto okamžiku jsou nezávislé třídy (takže klíč a tajný klíč načtený klientem trezoru klíčů neimplementují IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Šifrování objektu blob a nahrání

Přidejte následující kód, abyste zašifrovat objekt blob a nahrát ho do svého účtu úložiště Azure. Metoda **ResolveKeyAsync,** která se používá vrátí IKey.

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

> [!NOTE]
> Pokud se podíváte na konstruktor blobEncryptionPolicy, uvidíte, že může přijmout klíč nebo překládání. Uvědomte si, že právě teď nelze použít překládání pro šifrování, protože aktuálně nepodporuje výchozí klíč.

## <a name="decrypt-blob-and-download"></a>Dešifrování objektu blob a stažení

Dešifrování je opravdu při použití Resolver třídy smysl. ID klíče použitého pro šifrování je přidruženo k objektu blob v jeho metadatech, takže není důvod k načtení klíče a zapamatování si přidružení mezi klíčem a objektem blob. Stačí se ujistit, že klíč zůstane v trezoru klíčů.   

Soukromý klíč klíče RSA zůstane v trezoru klíčů, takže pro dešifrování dojde, šifrovaný klíč z metadat objektu blob, který obsahuje CEK je odeslána do trezoru klíčů k dešifrování.

Přidejte následující dešifrování objektu blob, který jste právě nahráli.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Existuje několik dalších druhů překladačů, které usnadňují správu klíčů, včetně: AggregateKeyResolver a CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Použití tajných kódů trezoru klíčů

Způsob použití tajného klíče s šifrováním na straně klienta je prostřednictvím třídy SymmetricKey, protože tajný klíč je v podstatě symetrický klíč. Ale, jak je uvedeno výše, tajný klíč v trezoru klíčů není mapovat přesně symmetricKey. Existuje několik věcí, které je třeba pochopit:

* Klíč v SymmetricKey musí být pevnou délku: 128, 192, 256, 384 nebo 512 bitů.
* Klíč v SymmetricKey by měl být Base64 kódován.
* Tajný klíč trezoru klíčů, který bude použit jako SymmetricKey musí mít typ obsahu "aplikace/oktet-stream" v trezoru klíčů.

Zde je příklad v prostředí PowerShell vytvoření tajného klíče v trezoru klíčů, který lze použít jako SymmetricKey.
Vezměte prosím na vědomí, že pevně zakódovaná hodnota, $key, je pouze pro demonstrační účely. Ve vlastním kódu budete chtít generovat tento klíč.

```csharp
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

V aplikaci konzoly můžete použít stejné volání jako dříve k načtení tohoto tajného klíče jako SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

A to je vše. Užijte si ji!

## <a name="next-steps"></a>Další kroky

Další informace o používání microsoft azure storage s C#najdete v [tématu Microsoft Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Další informace o rozhraní BLOB REST API najdete v [tématu rozhraní REST API služby blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Nejnovější informace o microsoft azure storage najdete na [blogu Microsoft Azure Storage Team .](https://blogs.msdn.com/b/windowsazurestorage/)
