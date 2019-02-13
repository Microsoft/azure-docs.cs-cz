---
title: 'Kurz: Šifrování a dešifrování objektů BLOB v Azure Storage pomocí Azure Key Vault | Dokumentace Microsoftu'
description: Postup šifrování a dešifrování objektu blob s využitím šifrování na straně klienta pro službu Microsoft Azure Storage s Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4552249e7d7dd79edbe885b3d615f5071aa694ee
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116095"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Kurz: Šifrování a dešifrování objektů BLOB ve službě Microsoft Azure Storage pomocí Azure Key Vault
## <a name="introduction"></a>Úvod
Tento kurz se zaměřuje na tom, jak provést pomocí šifrování na straně klienta úložiště se službou Azure Key Vault. Provede vás provede postupy šifrování a dešifrování objektů blob v konzolové aplikaci, použití těchto technologií.

**Odhadovaný čas dokončení:** 20 minut

Další informace o službě Azure Key Vault najdete v článku [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Další informace o šifrování na straně klienta pro službu Azure Storage, najdete v článku [šifrování na straně klienta a služby Azure Key Vault pro Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

* Účet služby Azure Storage
* Visual Studio 2013 nebo novější
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Přehled šifrování na straně klienta
Přehled šifrování na straně klienta pro službu Azure Storage najdete v tématu [šifrování na straně klienta a služby Azure Key Vault pro úložiště Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Tady je stručný popis toho, jak funguje šifrování na straně klienta:

1. Klient služby Azure Storage SDK generuje obsahu šifrovací klíč (CEK), což je použití jednoho jednorázových symetrický klíč.
2. Zákaznická data se šifrují pomocí této CEK.
3. CEK je vnořena (zašifrovaný) pomocí šifrovací klíč klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a mohou být asymetrický pár klíčů nebo symetrický klíč a může být spravovaný místně nebo uloženy ve službě Azure Key Vault. Klienta úložiště samotné nikdy má přístup k klíče KEK. Vyvolá pouze algoritmus klíče zabalení, která je k dispozici ve službě Key Vault. Zákazníci můžou zvolit pro klíč zabalení a rozbalení chtějí-li použít vlastní poskytovatele.
4. Šifrovaná data se pak nahrají do služby Azure Storage.

## <a name="set-up-your-azure-key-vault"></a>Nastavení služby Azure Key Vault
Chcete-li pokračovat s tímto kurzem, budete muset provést následující kroky, které jsou popsány v tomto kurzu [co je Azure Key Vault?](../../key-vault/key-vault-overview.md):

* Vytvoření trezoru klíčů
* Přidání klíče nebo tajného klíče do trezoru klíčů.
* Zaregistrujte aplikaci v Azure Active Directory.
* Autorizujte aplikaci pro použití klíče nebo tajného klíče.

Poznamenejte si ID klienta a ClientSecret, které byly generovány při registraci aplikace v Azure Active Directory.

Vytvoření oba klíče v trezoru klíčů. Pro zbývající část tohoto kurzu předpokládáme, že jste použili následující názvy: ContosoKeyVault a TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Vytvoření konzolové aplikace pomocí balíčků a AppSettings
V sadě Visual Studio vytvořte novou konzolovou aplikaci.

Přidání balíčků nuget nezbytné v konzole Správce balíčků.

```
Install-Package WindowsAzure.Storage
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Přidáte AppSettings do App.Config.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Přidejte metodu k získání tokenu do konzolové aplikace
Následující metoda se používá ve službě Key Vault třídy, které potřebujete k ověření pro přístup k trezoru klíčů.

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

## <a name="access-storage-and-key-vault-in-your-program"></a>Přístup k úložišti a Key Vault ve svém programu
Ve funkci Main přidejte následující kód.

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
> Objektové modely služby Key Vault
> 
> Je důležité pochopit, že jsou ve skutečnosti dvě služby Key Vault objektové modely zajímat: jeden je založená na rozhraní REST API (obor názvů služby KeyVault) a druhý je rozšířením pro šifrování na straně klienta.
> 
> Klient klíče trezoru komunikuje pomocí rozhraní REST API a rozumí JSON Web klíče a tajné kódy pro dva druhy věcí, které jsou obsaženy ve službě Key Vault.
> 
> Rozšíření Key Vault jsou třídy, které vypadá to, že vytvořený specificky pro šifrování na straně klienta ve službě Azure Storage. Obsahují rozhraní pro třídy založena na konceptu překladače klíč a klíče (Instrumentační klíč). Existují dvě implementace Instrumentační klíč, který je potřeba vědět: RSAKey a SymmetricKey. Teď k nim dojde, aby odpovídala věcí, které jsou obsaženy ve službě Key Vault, ale v tuto chvíli jsou nezávislé třídy (takže klíč a tajný kód načtený klient trezoru klíč neimplementují Instrumentační klíč).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Šifrování objektů blob a odeslat
Přidejte následující kód k šifrování objektu blob a nahrajte ho do svého účtu úložiště Azure. **ResolveKeyAsync** metodu, která se používá vrátí Instrumentační klíč.

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
> Když se podíváte na konstruktor BlobEncryptionPolicy, uvidíte, že může přijmout klíč a/nebo překladače. Mějte na paměti, které chvíli překladače nelze použít pro šifrování, protože není aktuálně provádí podporují výchozí klíč.
> 
> 

## <a name="decrypt-blob-and-download"></a>Dešifrování objektů blob a stáhnout
Dešifrování je ve skutečnosti když použitím překladač třídy dávat smysl. ID klíč používaný k šifrování je přidružený objekt blob ve svých metadatech, takže neexistuje žádný důvod k načtení klíče a nezapomeňte přidružení mezi klíčem a objektů blob. Stačí Ujistěte se, že klíč zůstane ve službě Key Vault.   

Privátní klíč klíčem RSA zůstává ve službě Key Vault, takže k dešifrování dojde k zašifrovaný klíč z metadat objektu blob, který obsahuje CEK se odešlou do služby Key Vault k dešifrování.

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
> Existuje několik dalších typů překladače pro usnadnění správy klíčů, včetně: AggregateKeyResolver a CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>Použití tajných kódů služby Key Vault
Způsob, jak pomocí šifrování na straně klienta tajný kód je prostřednictvím třídy SymmetricKey, protože tajného kódu je v podstatě symetrický klíč. Ale jak bylo uvedeno výše, tajný klíč ve službě Key Vault není namapovaná na SymmetricKey přesně. Chcete-li pochopit některé věci:

* Klíč SymmetricKey musí být pevné délky: 128, 192, 256, 384 nebo 512 bitů.
* Klíč v SymmetricKey by měl být kódovaný ve formátu Base64.
* Key Vault tajný klíč, který se použije jako SymmetricKey musí mít obsah typ "application/octet-stream" ve službě Key Vault.

Tady je příklad v prostředí PowerShell ve službě Key Vault, který může sloužit jako SymmetricKey vytvoření tajného kódu.
Všimněte si, že pevně zakódované hodnota $key, je pro demonstrační účely pouze. Ve svém vlastním kódu budete chtít vygenerovat tento klíč.

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

Z vaší konzolové aplikace můžete použít stejné volání jako před načíst tento tajný kód jako SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
A to je vše. Užijte si ji!

## <a name="next-steps"></a>Další postup
Další informace o používání služby Microsoft Azure Storage pomocí jazyka C# najdete v tématu [Microsoft Azure Storage Client Library pro .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Další informace o rozhraní REST API objektů Blob najdete v tématu [rozhraní REST API služby Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Nejnovější informace o Microsoft Azure Storage, přejděte [Blog týmu Microsoft Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/).
