---
ms.assetid: ''
title: Klíče účtu úložiště Azure Key Vault
description: Klíče účtu úložiště poskytují seemless integrace mezi službami Azure Key Vault a klíče přístupu na základě do účtu úložiště Azure.
ms.topic: article
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 08/21/2017
ms.openlocfilehash: 0112d48647c031845bc89ccebfcdd40954c59f14
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054267"
---
# <a name="azure-key-vault-storage-account-keys"></a>Klíče účtu úložiště Azure Key Vault

Vývojáři se před klíčů k účtu Azure Key Vault úložiště, museli spravovat své vlastní klíče účtu úložiště Azure (ASA) a otočení je ručně nebo přes externí služby automation. Nyní, klíče účtu úložiště Key Vault se implementují jako [tajných kódů služby Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) pro ověřování pomocí účtu služby Azure Storage.

Klíčovou funkcí účet úložiště Azure (ASA) slouží ke správě tajných kódů otočení za vás. Zároveň se tím odebere potřebu přímém kontaktu s klíčem ASA nabídkou sdílených přístupových podpisů (SAS) jako metodu.

Další obecné informace o účtech Azure Storage, najdete v části [účty Azure storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Podpora rozhraní

Najdete úplný seznam a odkazy na naše skriptovací a programovací rozhraní [– Příručka vývojáře pro Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Co spravuje služby Key Vault

Při použití spravovaných klíčů účtu úložiště služby Key Vault provádí několik interních funkcí správy vaším jménem.

- Azure Key Vault se spravuje klíče z Azure Storage účtu (ASA).
    - Azure Key Vault interně, můžete zobrazit seznam klíčů (sync) pomocí účtu služby Azure Storage.
    - Služba Azure Key Vault obnoví (obmění) klíče pravidelně.
    - Hodnoty klíče se nikdy vrátí odpověď na volajícího.
    - Služba Azure Key Vault slouží ke správě klíčů účtů úložiště a klasické účty úložiště.
- Služba Azure Key Vault umožňuje vás, vlastníka trezoru nebo objekt, k vytvoření definice SAS (sdíleným přístupovým podpisem, účtu nebo SAS služby).
    - SAS, vytvořené pomocí definice SAS, je vrácena jako tajný kód prostřednictvím cesty identifikátoru URI REST. Další informace najdete v tématu operací definice SAS [REST API služby Azure Key Vault odkaz](/rest/api/keyvault).

## <a name="naming-guidance"></a>Pokyny pro pojmenování

- Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Název definice SAS musí být 1-102 znaků obsahující pouze 0-9, a – z, A až Z.

## <a name="developer-experience"></a>Prostředí pro vývojáře

### <a name="before-azure-key-vault-storage-keys"></a>Před klíčů k úložišti Azure Key Vault

Vývojáři použít k potřeba provést následující postupy se klíče účtu úložiště se získat přístup ke službě Azure storage.
1. V nastavení aplikace služby Azure App Service nebo jiného úložiště Store připojovacího řetězce nebo tokenu SAS.
1. Při spouštění aplikace načtení připojovacího řetězce nebo tokenu SAS.
1. Vytvoření [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) k interakci s úložištěm.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Po klíčů k úložišti Azure Key Vault

Vývojáři vytvářet [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) a využít k získání tokenu SAS pro jejich úložiště. Potom se vytvářejí [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) pomocí tohoto tokenu.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Informace pro vývojáře

- Povolte pouze Key Vault a spravovat klíče pro Azure Stream Analytics. Nepokoušejte se spravovat sami, koliduje s procesy služby Key Vault.
- Nepovolit klíče Azure Stream Analytics lze spravovat pomocí více než jeden objekt služby Key Vault.
- Pokud je potřeba ručně znovu vygenerovat klíče Azure Stream Analytics, doporučujeme vám, můžete obnovit pomocí služby Key Vault.

## <a name="getting-started"></a>Začínáme

### <a name="give-key-vault-access-to-your-storage-account"></a>Poskytnout službě Key Vault přístup k vašemu účtu úložiště 

Stejně jako mnoho aplikací je Key Vault v Azure AD zaregistrováno Chcete-li použít OAuth pro přístup k dalším službám. Během registrace [hlavního názvu služby](/azure/active-directory/develop/app-objects-and-service-principals) je vytvořen objekt, který se používá k reprezentování identitu aplikace v době běhu. Instanční objekt se používá také k autorizaci identitu pro přístup k jinému zdroji, prostřednictvím řízení přístupu na základě role (RBAC).

Identita aplikace služby Azure Key Vault potřebuje oprávnění k *seznamu* a *znovu vygenerovat* klíče účtu úložiště. Nastavení těchto oprávnění pomocí následujících kroků:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get Application ID of Azure Key Vault's service principal
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Funkční příklad

Následující příklad ukazuje vytvoření služby Key Vault spravovat účet služby Azure Storage a přidružené definice SAS.

### <a name="prerequisite"></a>Požadavek

Zkontrolujte jste dokončili [nastavit oprávnění řízení přístupu na základě role](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Nastavení

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Dále nastavte oprávnění pro **účtu** zajistit, že můžete spravovat všechna úložiště oprávnění ve službě Key Vault. V následujícím příkladu je náš účet Azure _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Vytvoření trezoru klíčů spravovaného účtu úložiště

Teď vytvořte spravovaný účet úložiště ve službě Azure Key Vault a přístupového klíče z vašeho účtu úložiště používat k vytváření tokenů SAS.
- `-ActiveKeyName` ke generování tokenů SAS používá "key2".
- `-AccountName` slouží k identifikaci účtu spravovaného úložiště. Níže pro jednoduchost používáme název účtu úložiště, ale může být jakýkoli název.
- `-DisableAutoRegenerateKey` Určuje, ne obnovit klíče účtu úložiště.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Opětovném generování přístupového klíče

Pokud chcete služby Key Vault se pravidelně znovu vygenerovat přístupové klíče vašeho úložiště, můžete nastavit dobu obnovení. Níže jsme nastavujete opětovné generování období 3 dní. Po 3 dnech se služby Key Vault znovu vygenerovat "key1" a Prohodit aktivní klíč z "key2" k "key1".

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Definice SAS sady

SAS účtu poskytuje přístup ke službě blob s různými oprávněními.
Nastavení definice SAS ve službě Key Vault pro váš účet spravovaného úložiště.
- `-AccountName` je název účtu úložiště spravované ve službě Key Vault.
- `-Name` je identifikátor pro token SAS ve svém úložišti.
- `-ValidityPeriod` Nastaví datum vypršení platnosti vygenerovaný token SAS.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Získat tokeny SAS

Získejte odpovídající tokeny SAS a využijeme volání do úložiště. `-SecretName` je vytvořen pomocí vstupu z `AccountName` a `Name` parametry, když jste spustili [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Vytvořit úložiště

Všimněte si, že se pokoušíte získat přístup pomocí *$readSasToken* selže, ale, že máme přístup pomocí *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

# Ensure the txt file in command exists in local path mentioned
Set-AzureStorageBlobContent -Container containertest1 -File "./abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "./file.txt" -Context $context2
```

Máte přístup k obsahu s tokenem SAS, který má oprávnění k zápisu objektů blob úložiště.

### <a name="relevant-powershell-cmdlets"></a>Odpovídající rutiny prostředí Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Připojení účtu úložiště

Příklad: jako vlastník objektu služby Key Vault, které přidáte objekt účtu úložiště do služby Azure Key Vault k připojení účtu úložiště.

Během registrace, je potřeba služby Key Vault ověřte, zda má identita registrace účtu oprávnění k *seznamu* a *znovu vygenerovat* klíče úložiště. Aby bylo možné ověřit, tato oprávnění, služby Key Vault získá OBO (na Behalf Of) token z ověřovací služby, cílové skupiny nastavit na Azure Resource Manageru a díky *seznamu* klíče volání služby Azure Storage. Pokud *seznamu* volání selže, služby Key Vault k vytvoření objektu selže s kódem stavu HTTP *zakázáno*. Jsou uložené v mezipaměti klíče uvedené tímto způsobem s vaší entity úložiště služby key vault.

Key Vault musí ověřit, zda má identita *znovu vygenerovat* oprávnění předtím, než ji můžou převzít vlastnictví klíče. Chcete-li ověřit, že tato oprávnění má identitu prostřednictvím tokenu OBO, stejně jako první strany identita služby Key Vault:

- Key Vault seznam oprávnění RBAC na prostředek účtu úložiště.
- Key Vault ověří odpovědi pomocí regulárního výrazu porovnání a jiných akcí.

Najít příklady podpůrné na [služby Key Vault – spravované ukázky klíče účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=).

Pokud identita nemá *znovu vygenerovat* oprávnění nebo pokud Key Vault první strany identita nemá *seznamu* nebo *znovu vygenerovat* oprávnění a pak registrace požadavek selže, vrátí kód příslušné chyby a zprávy.

OBO token, který bude fungovat jenom při použití první třetích stran, nativní klientské aplikace z Powershellu nebo rozhraní příkazového řádku.

## <a name="other-applications"></a>Další aplikace

- Tokeny SAS, vytvářeny pomocí klíčů k účtu úložiště služby Key Vault, zadejte ještě více řízený přístup k účtu služby Azure storage. Další informace najdete v tématu [použití sdílených přístupových podpisů](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Další informace najdete v tématech

- [Informace o klíčích, tajných kódech a certifikátech](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog týmu služby Key Vault](https://blogs.technet.microsoft.com/kv/)
