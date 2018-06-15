---
title: 'Kurz: Šifrování a dešifrování objektů BLOB v úložišti Azure pomocí Azure Key Vault | Microsoft Docs'
description: Jak šifrování a dešifrování objektu blob pomocí šifrování na straně klienta pro úložiště Microsoft Azure s Azure Key Vault.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: adhurwit
ms.openlocfilehash: 405ccb44c9daf8d555946e6c68ef318ed2b82505
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2018
ms.locfileid: "27815809"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Kurz: Šifrování a dešifrování objektů BLOB v úložišti Microsoft Azure pomocí Azure Key Vault
## <a name="introduction"></a>Úvod
Tento kurz se zaměřuje na postup ověření pomocí šifrování na straně klienta úložiště s Azure Key Vault. Provede vás provedou postupem pro šifrování a dešifrování objektů blob v konzolové aplikaci použití těchto technologií.

**Odhadovaný čas dokončení:** 20 minut

Souhrnné informace o Azure Key Vault naleznete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Další informace o šifrování na straně klienta pro Azure Storage najdete v části [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

* Účet úložiště Azure
* Visual Studio 2013 nebo novější
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Přehled šifrování na straně klienta
Přehled šifrování na straně klienta pro Azure Storage najdete v tématu [šifrování na straně klienta a Azure Key Vault pro úložiště Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Zde je stručný popis toho, jak funguje šifrování na straně klienta:

1. Klient Azure Storage SDK vytvoří obsahu šifrovací klíč (CEK), což je použití jednoho bránu symetrického klíče.
2. Zákaznická data zašifrována pomocí této CEK.
3. CEK je vnořena (šifrované) pomocí klíčů šifrovacího klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a může být pár asymetrických klíčů nebo symetrického klíče a mohou být spravovaný místně nebo uloženy v Azure Key Vault. Klient úložiště pro samotné nikdy má přístup k klíče KEK. Vyvolá právě algoritmus zabalení klíče, který zajišťuje Key Vault. Tato volba se pro klíč zabalení/rozbalování Pokud chtějí použít vlastního zprostředkovatele.
4. Šifrovaná data se pak odešlou do služby Azure Storage.

## <a name="set-up-your-azure-key-vault"></a>Nastavení Azure Key Vault
Chcete-li pokračovat v tomto kurzu, je třeba provést následující kroky, které jsou uvedeny v tomto kurzu [Začínáme s Azure Key Vault](../../key-vault/key-vault-get-started.md):

* Vytvoření trezoru klíčů.
* Přidejte klíče nebo tajného klíče do trezoru klíčů.
* Zaregistrujte aplikaci s Azure Active Directory.
* Autorizovat aplikaci pro použití klíče nebo tajného klíče.

Poznamenejte si ClientID a ClientSecret, které byly generovány při registraci aplikace na Azure Active Directory.

Vytvořte oba klíče v trezoru klíčů. Pro zbývající části tohoto kurzu předpokládáme, že jste použili následující názvy: ContosoKeyVault a TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Vytvořte konzolovou aplikaci s balíčky a AppSettings
V sadě Visual Studio vytvořte novou konzolovou aplikaci.

Přidání balíčků nuget potřebné v konzole Správce balíčků.

```
Install-Package WindowsAzure.Storage

// This is the latest stable release for ADAL.
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Přidejte AppSettings souboru App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Přidejte následující `using` direktivy a ujistěte se, že do projektu přidejte odkaz na System.Configuration.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;        
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Přidejte metodu k získání tokenu pro konzolové aplikace
Následující metoda se používá Key Vault třídy, které potřebují k ověřování pro přístup k trezoru klíčů.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        ConfigurationManager.AppSettings["clientId"],
        ConfigurationManager.AppSettings["clientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-storage-and-key-vault-in-your-program"></a>Přístup k úložišti a Key Vault v programu
Hlavní funkce přidejte následující kód.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    ConfigurationManager.AppSettings["accountName"],
       ConfigurationManager.AppSettings["accountKey"]);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Trezor klíčů objektové modely
> 
> Je důležité si uvědomit, že jsou ve skutečnosti dvě Key Vault objektové modely zajímat: jeden je založená na volání rozhraní API REST (KeyVault obor názvů) a druhá je rozšířením pro šifrování na straně klienta.
> 
> Klíč trezoru klient komunikuje se službou REST API a porozuměl jim JSON webové klíče a tajné klíče pro dva druhy věcí, které jsou obsaženy v Key Vault.
> 
> Klíč trezoru rozšíření jsou třídy, které zdá se, že vytvořený specificky pro šifrování na straně klienta ve službě Azure Storage. Obsahují rozhraní pro třídy podle konceptu překladač klíč a klíče (IKey). Existují dvě implementace IKey, které je nutné znát: RSAKey a SymmetricKey. Nyní dějí se shoduje s věcí, které jsou obsaženy v Key Vault, ale v tomto okamžiku jsou nezávislé třídy (tak klíč a tajný klíč načíst klíč trezoru klient neimplementuje IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Šifrování objektů blob a nahrajte
Přidejte následující kód k šifrování objekt blob a nahrajte ho do účtu úložiště Azure. **ResolveKeyAsync** vrátí metoda, která se používá IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
// Remember that we used the names contosokeyvault and testrsakey1.
var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Pokud se podíváte na BlobEncryptionPolicy konstruktoru, uvidíte, že může přijmout klíč nebo překladač. Mějte na paměti, které tuto chvíli nemůžete použít překladač pro šifrování, protože není aktuálně dělá podporují výchozí klíč.
> 
> 

## <a name="decrypt-blob-and-download"></a>Dešifrování objektů blob a stáhnout
Dešifrování je ve skutečnosti při používání tříd překladač smysl. ID klíče pro šifrování je přidružený objekt blob ve svých metadatech, takže neexistuje žádný důvod pro načíst klíč a nezapomeňte přidružení mezi klíč a objektů blob. Musíte se ujistěte, že klíč zůstane v Key Vault.   

Privátní klíč klíč RSA zůstane v Key Vault, takže k dešifrování proběhnout, je zašifrovaná klíč z metadata objektu blob, který obsahuje CEK odeslán Key Vault pro dešifrování.

Přidejte následující příkaz pro dešifrování objektů blob, který jste právě nahráli.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Existuje několik dalších druhy překladače v zájmu snazší správy klíčů, včetně: AggregateKeyResolver a CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>Pomocí Key Vault tajné klíče
Za způsob, jak pomocí šifrování na straně klienta tajný klíč je prostřednictvím třídy SymmetricKey, protože v podstatě symetrický klíč je tajný klíč. Ale, jak je uvedeno výše, tajný klíč v Key Vault nejsou namapované přesně SymmetricKey. Existuje několik postupů k pochopení:

* Musí být pevnou délkou klíče v SymmetricKey: 128, 192, 256, 384 nebo 512 bitů.
* Klíče v SymmetricKey by měla mít kódování Base64.
* Key Vault tajný klíč, který se použije jako SymmetricKey musí mít obsahu typ "application/octet-stream" v Key Vault.

Tady je příklad v prostředí PowerShell v Key Vault, který lze použít jako SymmetricKey vytváření tajný klíč.
Upozorňujeme, že pevně zakódovaný hodnota, $key, je pro demonstrační účely jenom. V kódu budete chtít vygenerovat tento klíč.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Ve vaší aplikaci konzoly můžete použít stejné volání jako před načíst tento tajný klíč jako SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
A je to. Užijte si ji!

## <a name="next-steps"></a>Další postup
Další informace o používání služby Microsoft Azure Storage pomocí C#, najdete v části [Microsoft Azure Storage Client Library pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Další informace o rozhraní REST API objektů Blob najdete v tématu [rozhraní API REST služby objektů Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Nejnovější informace o úložišti Microsoft Azure, přejděte na [Blog týmu Azure Storage Microsoft](http://blogs.msdn.com/b/windowsazurestorage/).
