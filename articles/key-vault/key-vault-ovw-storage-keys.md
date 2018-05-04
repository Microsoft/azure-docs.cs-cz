---
ms.assetid: ''
title: Klíče účtu úložiště Azure Key Vault
description: Klíče účtu úložiště poskytují seemless integrace mezi Azure Key Vault a klíče založené na přístup k účtu úložiště Azure.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Klíče účtu úložiště Azure Key Vault

Před klíčů k účtu Azure Key Vault úložiště vývojáři museli spravovat vlastní klíče účtu úložiště Azure (ASA) a jejich otočení ručně nebo pomocí externího automatizace. Nyní, klíče účtu úložiště klíč trezoru jsou implementované jako [tajné klíče Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) pro ověřování pomocí účtu úložiště Azure.

Funkci klíče účtu úložiště Azure (ASA) spravuje tajný otočení za vás. Odebere taky potřeba přímém kontaktu s klíčem ASA prostřednictvím nabídky sdíleného přístupu podpisy (SAS) jako metodu.

Další obecné informace o účtech úložiště Azure najdete v tématu [účty Azure storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Podpora rozhraní

Úplný seznam a odkazy na našem skriptovací a programovací rozhraní naleznete [Příručka vývojáře Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Co spravuje Key Vault

Při použití spravovaných klíče účtu úložiště Key Vault provede několik funkcí správy interní vaším jménem.

- Azure Key Vault spravuje klíče z Azure Storage účtu (ASA).
    - Azure Key Vault interně, můžete seznam klíčů (sync) s účtem úložiště Azure.
    - Azure Key Vault regeneruje (otočí) klíče pravidelně.
    - Hodnoty klíče se nikdy vrátí jako odpověď na volajícího.
    - Azure Key Vault spravuje klíče účtů úložiště a klasické účty úložiště.
- Azure Key Vault umožňuje vás, vlastníka trezoru nebo objekt, k vytvoření definice SAS (účet nebo SAS služby).
    - SAS, vytvořené pomocí definice SAS, je vrácena jako tajný klíč prostřednictvím cesta k identifikátoru URI REST. Další informace najdete v tématu [operace účet úložiště Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Pokyny pro pojmenování

- Názvy účtů úložiště musí mít od 3 do 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Název definice SAS musí být 1-102 znaků obsahující pouze 0 – 9,-z, A-Z.

## <a name="developer-experience"></a>Možnosti pro vývojáře

### <a name="before-azure-key-vault-storage-keys"></a>Před klíče úložiště Azure Key Vault

Vývojáři použít k proveďte následující postupy se klíč účtu úložiště a získat tak přístup k úložišti Azure.
1. Připojovací řetězec nebo tokenu SAS uložit v nastavení aplikace služby Azure App Service nebo jiného úložiště.
1. Při spouštění aplikace získat připojovací řetězec nebo tokenu SAS.
1. Vytvoření [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) k interakci s úložištěm.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Po klíče úložiště Azure Key Vault

Vývojáři vytvářet [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) a využít, který se získat SAS token pro jejich úložiště. Pak se vytvoří [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) pomocí tohoto tokenu.

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

 ### <a name="developer-guidance"></a>Průvodce pro vývojáře

- Povolte jenom spravovat klíče ASA Key Vault. Nepokoušejte se spravovat sami, bude narušovat procesy Key Vault.
- Nepovolit ASA klíče, který bude spravován více než jeden objekt Key Vault.
- Pokud budete muset ručně znovu vygenerovat klíče ASA, doporučujeme vám obnovit prostřednictvím Key Vault.

## <a name="getting-started"></a>Začínáme

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Instalační program pro oprávnění k přístupu na základě rolí k řízení (RBAC)

Identita aplikace Azure Key Vault potřebuje oprávnění k *seznamu* a *znovu vygenerovat* klíče pro účet úložiště. Nastavte tato oprávnění pomocí následujících kroků:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Příklad práce

Následující příklad ukazuje, že vytvoření trezoru klíč spravovaný účet úložiště Azure a přidružené definice sdíleného přístupového podpisu (SAS).

### <a name="prerequisite"></a>Požadavek

Ujistěte se, když jste dokončili [nastavení pro oprávnění k přístupu na základě rolí k řízení (RBAC)](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Nastavení

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Dále nastavte oprávnění pro **účtu** zajistit, že můžete spravovat všechna oprávnění úložišť v Key Vault. V následujícím příkladu je naše účet Azure _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Vytvoření trezoru klíčů spravovaný účet úložiště

Nyní vytvořit účet úložiště spravované v Azure Key Vault a přístupový klíč z vašeho účtu úložiště používat k vytváření tokenů SAS.
- `-ActiveKeyName` 'key2' se používá ke generování tokenů SAS.
- `-AccountName` slouží k identifikaci vašeho účtu úložiště spravovaný. Níže název účtu úložiště se používá pro jednoduchost, ale může být jakýkoli název.
- `-DisableAutoRegenerateKey` Určuje není obnovit klíče účtu úložiště.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Opětovné generování klíče

Pokud chcete znovu vygenerovat přístupové klíče vašeho úložiště pravidelně Key Vault, můžete nastavit opětovné generování období. Níže budeme se nastavení opětovné generování období 3 dní. Po 3 dny bude Key Vault znovu vygenerovat 'key1' a Prohodit active klíč z 'key2' do 'key1'.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Definice sady SAS

SAS účtu poskytuje přístup ke službě blob s jinými oprávněními.
Nastavte definice SAS v Key Vault pro váš účet úložiště spravovaný.
- `-AccountName` je název účtu úložiště spravovaný v Key Vault.
- `-Name` je identifikátor pro token SAS v úložišti.
- `-ValidityPeriod` Nastaví datum vypršení platnosti vygenerovaný token SAS.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Získat tokeny SAS

Získat odpovídající tokeny SAS a provádět volání do úložiště. `-SecretName` je vytvořený pomocí vstupu z `AccountName` a `Name` parametry při provedení [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Vytvořit úložiště

Všimněte si, že se pokoušíte získat přístup s *$readSasToken* selže, ale, že jsme mají mít přístup s *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Budete mít přístup k obsahu s tokenem SAS, který má oprávnění k zápisu objektu blob storage.

### <a name="relevant-powershell-cmdlets"></a>Příslušné rutiny prostředí Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Registrace účtu úložiště

Příklad: jako vlastník objektu Key Vault, které přidáte objekt účtu úložiště Azure Key Vault pro zaváděním účet úložiště.

Během registrace, Key Vault musí ověřit, že identitu účtu registrace má oprávnění k *seznamu* a *znovu vygenerovat* klíče úložiště. Chcete-li ověřit, tato oprávnění, Key Vault získá OBO (na Behalf Of) token pomocí ověřovací služby, cílové skupiny nastavená na Azure Resource Manager a díky *seznamu* klíče volání služby Azure Storage. Pokud *seznamu* volání selže, Key Vault vytvoření objektu selže s kódem stavu HTTP *zakázáno*. S úložištěm entity váš trezor klíčů v mezipaměti klíče uvedené tímto způsobem.

Key Vault musí ověřte, zda má identita *znovu vygenerovat* oprávnění předtím, než ji může převzít vlastnictví opětovné generování klíče. Chcete-li ověřit, že identita, prostřednictvím OBO token, stejně jako první strany identita Key Vault má tato oprávnění:

- Key Vault seznam oprávnění RBAC na prostředků účtu úložiště.
- Key Vault ověří odpovědi pomocí regulárního výrazu shody akce a jiné akce.

Najít příklady podpůrné v [Key Vault - spravované ukázky klíče účtu úložiště](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Není-li identitu *znovu vygenerovat* oprávnění nebo pokud Key Vault první strany identity nemá *seznamu* nebo *znovu vygenerovat* oprávnění a potom registrace požadavek selže, vrátí kód odpovídající chyby a zprávy.

OBO token bude fungovat pouze při použití vlastních, aplikace nativního klienta PowerShell nebo rozhraní příkazového řádku.

## <a name="other-applications"></a>Ostatní aplikace

- Tokeny SAS, vytvářeny pomocí klíčů k účtu úložiště Key Vault, zadejte i více řízený přístup k účtu úložiště Azure. Další informace najdete v tématu [pomocí sdílené přístupové podpisy](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Další informace najdete v tématech

- [Informace o klíčích, tajných kódech a certifikátech](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog týmu trezoru klíčů](https://blogs.technet.microsoft.com/kv/)
