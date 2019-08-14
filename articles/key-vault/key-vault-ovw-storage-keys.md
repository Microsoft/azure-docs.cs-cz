---
title: Správa klíčů účtu úložiště pomocí Azure Key Vault a Azure CLI
description: Klíče účtu úložiště umožňují bezproblémovou integraci mezi Azure Key Vault a klíčovým přístupem k účtu služby Azure Storage.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 7ba85d74f9126f4586313dc4e2b365d1e11f3798
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934176"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Správa klíčů účtu úložiště pomocí Azure Key Vault a Azure CLI 

Azure Key Vault spravuje klíče pro účty úložiště Azure a účty klasického úložiště. K dokončení několika funkcí správy klíčů můžete použít funkci spravovaného účtu úložiště Key Vault.

[Účet úložiště Azure](/azure/storage/storage-create-storage-account) používá přihlašovací údaje, které se skládají z názvu účtu a klíče. Klíč se vygeneruje automaticky a slouží jako heslo, nikoli jako kryptografický klíč. Key Vault spravuje klíče účtu úložiště tak, že je ukládá jako [Key Vault tajných klíčů](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Klíče jsou uvedené (synchronizované) s účtem služby Azure Storage a jsou pravidelně znovu vygenerovány nebo _otočeny_. 

Když použijete funkci klíče spravovaného účtu úložiště, vezměte v úvahu následující body:

- Hodnoty klíče se nikdy nevrátí jako odpověď volajícímu.
- Klíče účtu úložiště by měly spravovat jenom Key Vault. Nespravujte klíče sami a zabraňte v narušování Key Vaultch procesů.
- Klíče účtu úložiště by měl spravovat jenom jeden objekt Key Vault. Nepovoluje správu klíčů z více objektů.
- Můžete požádat o Key Vault spravovat účet úložiště pomocí objektu zabezpečení uživatele, ale ne pomocí instančního objektu.
- Obnovte klíče jenom pomocí Key Vault. Neobnovujte ručně klíče účtu úložiště. 

> [!NOTE]
> Azure Storage integrace s Azure Active Directory (Azure AD) je cloudová služba pro správu identit a přístupu od Microsoftu.
> Integrace Azure AD je k dispozici pro [objekty BLOB a fronty Azure](../storage/common/storage-auth-aad.md).
> K ověřování a autorizaci použijte Azure AD.
> Azure AD poskytuje přístup založený na tokenech OAuth2 Azure Storage stejně jako Azure Key Vault.
>
> Azure AD umožňuje ověřování klientské aplikace pomocí identity aplikace nebo uživatele místo přihlašovacích údajů k účtu úložiště.
> Při spuštění v Azure můžete použít [spravovanou identitu Azure AD](/azure/active-directory/managed-identities-azure-resources/) . Spravované identity odstraňují nutnost ověřování klientů a ukládání přihlašovacích údajů do aplikace nebo s vaší aplikací.
> Azure AD používá řízení přístupu na základě role (RBAC) ke správě autorizací, která je taky podporovaná Key Vault.

### <a name="service-principal-application-id"></a>ID aplikace instančního objektu

Tenant [služby](/azure/active-directory/develop/developer-glossary#service-principal-object)Azure AD poskytuje každou registrovanou aplikaci s instančním objektem. Instanční objekt slouží jako identita aplikace (ID). ID aplikace se používá během autorizačního nastavení pro přístup k jiným prostředkům Azure prostřednictvím RBAC.

Key Vault je aplikace Microsoftu, která je předem registrovaná ve všech klientech Azure AD. Key Vault je zaregistrovaný pod stejným ID aplikace a v rámci každého cloudu Azure.

| Tenantů | Cloud | ID aplikace |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Veřejné Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Ostatní  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Požadavky

Než použijete Key Vault ke správě klíče účtu úložiště, přečtěte si požadavky:

- Nainstalujte [rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Vytvořte [účet úložiště Azure](https://azure.microsoft.com/services/storage/). Postupujte podle [těchto kroků](../storage/index.yml).
- Název účtu úložiště musí obsahovat jenom malá písmena a číslice. Název musí mít délku 3 až 24 znaků.        
      
## <a name="manage-storage-account-keys"></a>Správa klíčů účtu úložiště

Existují čtyři základní kroky použití Key Vault ke správě klíčů účtu úložiště:

1. Získá existující účet úložiště.
1. Načte existující Trezor klíčů.
1. Přidejte do trezoru účet spravovaného úložiště Key Vault. Nastaví `key1` se jako aktivní klíč s dobou obnovení 180 dnů.
1. Použijte `key1` k nastavení kontextu úložiště pro zadaný účet úložiště.

> [!NOTE]
> Key Vault jako služba jsou přiřazena oprávnění operátora pro váš účet úložiště.
> 
> Po nastavení Azure Key Vault klíčů spravovaného účtu úložiště se klíče jenom mění pomocí Key Vault.
> U spravovaných klíčů účtu úložiště Key Vault spravuje rotaci klíče účtu úložiště.

1. Po vytvoření účtu úložiště spusťte následující příkaz, který získá ID prostředku účtu úložiště, který chcete spravovat:
    ```
    az storage account show -n storageaccountname
    ```

    Zkopírujte hodnotu ID prostředku z výstupu příkazu:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Příklad výstupu:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Přiřaďte roli RBAC role služby klíčového účtu úložiště Key Vault. Tato role omezuje rozsah přístupu na váš účet úložiště. Pro klasický účet úložiště použijte roli role služby operátora klasický účet úložiště.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`je ID objektu pro Key Vault ve veřejném cloudu Azure. Chcete-li získat ID objektu pro Key Vault v cloudu Azure Government, přečtěte si téma [ID aplikace instančního objektu](#service-principal-application-id).
    
1. Vytvořte účet spravovaného úložiště Key Vault:

    Nastavte dobu regenerace 90 dní. Po 90 dnech Key Vault znovu vygeneruje `key1` a zahodí aktivní klíč z `key2` na `key1`. `key1`je pak označen jako aktivní klíč. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Vytváření a generování tokenů

Můžete také požádat Key Vault o generování tokenů sdíleného přístupového podpisu. Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Klientům můžete udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. Sdílený přístupový podpis poskytuje zabezpečený způsob, jak sdílet prostředky úložiště bez narušení klíčů účtu.

Příkazy v této části dokončí následující akce:

- Nastavte definici `<YourSASDefinitionName>`sdíleného přístupového podpisu účtu. Definice je nastavená na spravovaném účtu `<YourStorageAccountName>` úložiště Key Vault ve vašem trezoru `<VaultName>`klíčů.
- Vytvořte token sdíleného přístupového podpisu účtu pro služby blob, File, Table a Queue. Token se vytvoří pro službu typů prostředků, kontejner a objekt. Token se vytvoří se všemi oprávněními přes HTTPS a zadaným počátečním a koncovým datem.
- V trezoru nastavte Key Vault definici sdíleného přístupového podpisu spravovaného úložiště. Definice obsahuje identifikátor URI šablony tokenu sdíleného přístupového podpisu, který byl vytvořen. Definice má typ `account` sdíleného přístupového podpisu a je platná po dobu N dní.
- Načte skutečný přístupový token z Key Vault tajného klíče, který odpovídá definici sdíleného přístupového podpisu.

Po dokončení kroků v předchozí části spusťte následující příkazy, které požádají Key Vault, aby vygenerovaly tokeny sdíleného přístupového podpisu. 

1. Vytvořte definici sdíleného přístupového podpisu. Po vytvoření definice sdíleného přístupového podpisu požádejte Key Vault, aby vygeneroval další tokeny sdíleného přístupového podpisu. Tato operace vyžaduje `storage` oprávnění a `setsas` .
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Nápovědu k této operaci najdete v referenční dokumentaci [AZ Storage Account Generate-SAS](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) .

    Po úspěšném spuštění operace zkopírujte výstup.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. `$sasToken` Použijte vygenerovanou příkazem předchozí a vytvořte definici sdíleného přístupového podpisu. Další informace o parametrech příkazu najdete v tématu [AZ datatrezor Storage SAS-definition Create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) reference Document.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Pokud uživatel nemá oprávnění k účtu úložiště, nejdřív Získejte ID objektu uživatele:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Načíst tokeny v kódu

Pomocí Key Vault načíst [tokeny sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) ze služby Storage a provést operace v účtu úložiště.

Existují tři způsoby, jak ověřit Key Vault:

- Použijte identitu spravované služby. Tento přístup se důrazně doporučuje.
- Použijte instanční objekt a certifikát. 
- Použijte instanční objekt a heslo. Tento přístup se nedoporučuje.

Další informace najdete v tématu [Azure Key Vault: Základní koncepty](key-vault-whatis.md#basic-concepts).

Následující příklad ukazuje, jak načíst tokeny sdíleného přístupového podpisu. Tokeny se načítají po vytvoření definice sdíleného přístupového podpisu. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Pokud se brzo vyprší platnost tokenu sdíleného přístupového podpisu, načtěte znovu token sdíleného přístupového podpisu z Key Vault a aktualizujte kód.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Příkazy Azure CLI

Informace o příkazech rozhraní příkazového řádku Azure CLI, které jsou relevantní pro spravované účty úložiště, najdete v referenční dokumentaci k [úložišti klíčů AZ klíčů trezor](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [klíčích, tajných klíčích a certifikátech](https://docs.microsoft.com/rest/api/keyvault/).
- Projděte si články na [blogu Azure Key Vault týmu](https://blogs.technet.microsoft.com/kv/).
