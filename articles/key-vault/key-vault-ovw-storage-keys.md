---
title: Správa klíčů účtu úložiště pomocí Azure Key Vault a Azure CLI
description: Klíče účtu úložiště poskytují bezproblémovou integraci Azure Key Vault a na základě klíčů přístup k účtu služby Azure storage.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476148"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Správa klíčů účtu úložiště pomocí Azure Key Vault a Azure CLI 

Služba Azure Key Vault slouží ke správě klíčů pro účty služby Azure storage a účty klasického úložiště. Funkce účtu spravovaného úložiště služby Key Vault můžete dokončit několik funkcí správy klíčů.

[Účtu služby Azure storage](/azure/storage/storage-create-storage-account) používá pověření, která se skládá z názvu účtu a klíč. Klíč se automaticky generuje a slouží jako heslo, nikoli jako kryptografický klíč. Key Vault spravuje klíče účtu úložiště jako ukládání [tajných kódů služby Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Klíče jsou uvedeny (synchronizovaná) s účtem úložiště Azure a budou pravidelně znovu vygenerovány nebo _otočený_. 

Při použití funkce klíče účtu úložiště spravovaný, zvažte následující body:

- Hodnoty klíče se nikdy vrátí odpověď na volajícího.
- Pouze služby Key Vault měli spravovat své klíče účtu úložiště. Nemusíte spravovat klíče a zabránit konfliktům s procesy služby Key Vault.
- Klíče účtu úložiště by měl spravovat pouze jeden objekt služby Key Vault. Nepovolit správy klíčů z více objektů.
- Můžete požádat o službě Key Vault ke správě vašeho účtu úložiště pomocí objektu zabezpečení uživatele, ale ne s instančního objektu.
- Znovu vygenerovat klíče pouze pomocí služby Key Vault. Negenerovat ručně své klíče účtu úložiště. 

> [!NOTE]
> Integrace služby Azure Storage s Azure Active Directory (Azure AD) je služba Microsoftu pro cloudový identit a přístupu správy.
> Integrace se službou Azure AD je k dispozici pro [objekty BLOB Azure a fronty](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Pomocí služby Azure AD pro ověřování a autorizaci.
> Azure AD poskytuje přístupu na základě tokenu OAuth2 do služby Azure Storage, stejně jako Azure Key Vault.
>
> Azure AD umožňuje klientskou aplikaci ověřovat pomocí identitou aplikace nebo uživatele místo přihlašovacích údajů účtu úložiště.
> Můžete použít [identita spravované služby Azure AD](/azure/active-directory/managed-identities-azure-resources/) při spuštění v Azure. Spravované identity zbavují uživatele nutnosti pro ověřování klientů a ukládání přihlašovacích údajů v nebo s vaší aplikací.
> Řízení přístupu na základě rolí (RBAC) ke správě autorizace, což je také podporováno službou Key Vault používá Azure AD.

### <a name="service-principal-application-id"></a>ID aplikace instančního objektu služby

Tenanta služby Azure AD poskytuje každý registrované aplikaci [instanční objekt služby](/azure/active-directory/develop/developer-glossary#service-principal-object). Instanční objekt služby slouží jako identitu aplikace (ID). ID aplikace se používá během instalace autorizaci pro přístup k dalším prostředkům Azure přes RBAC.

Key Vault je aplikace Microsoftu, které je předem registrované v všechny klienty Azure AD. Key Vault je zaregistrovaný pod stejným ID aplikace a v rámci každé cloudu Azure.

| Tenanti | Cloud | ID aplikace |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Veřejný partnerský vztah Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Ostatní  | Jakýkoli | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Požadavky

Před použitím služby Key Vault ke správě vašeho klíče účtu úložiště, zkontrolujte požadavky:

- Nainstalujte [rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Vytvoření [účtu služby Azure storage](https://azure.microsoft.com/services/storage/). Postupujte podle [tyto kroky](https://docs.microsoft.com/azure/storage/).
- Název účtu úložiště musí používat jenom malá písmena a číslice. Délka názvu musí být dlouhý 3 až 24 znaků.        
      
## <a name="manage-storage-account-keys"></a>Správa klíčů účtu úložiště

Existují čtyři základní kroky pro použití služby Key Vault ke správě klíčů účtu úložiště:

1. Získáte existující účet úložiště.
1. Načte existujícího trezoru klíčů.
1. Přidání účtu úložiště služby Key Vault spravovat do trezoru. Nastavte `key1` jako aktivní klíč se znovu generovat období trvajícího 180 dní.
1. Použití `key1` nastavit kontext úložiště pro zadaný účet úložiště.

> [!NOTE]
> Jako služba Key Vault je přiřadit oprávnění operátora na vašem účtu úložiště.
> 
> Po nastavení Azure Key Vault spravovaných klíčů účtu úložiště změníte pouze klíče s použitím služby Key Vault.
> Key Vault pro spravované klíče účtu úložiště, spravuje otočení klíče účtu úložiště.

1. Po vytvoření účtu úložiště, spuštěním následujícího příkazu Získejte ID prostředku účtu úložiště pro správu:
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
    
1. Přiřaďte roli RBAC "Úložiště účtu klíč Role služby operátor" do služby Key Vault. Tato role omezuje obor přístupu k vašemu účtu úložiště. Pro klasický účet úložiště pomocí role "Klasického úložiště účtu klíč Role služby operátor".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` ID objektu je pro službu Key Vault ve veřejném cloudu Azure. K získání ID objektu pro Key Vault v cloudu Azure Government, najdete v článku [ID aplikace instančního objektu služby](#service-principal-application-id).
    
1. Vytvoření účtu úložiště spravované Key Vault:

    Nastavte interval obnovování 90 dnů. Po 90 dnech se znovu vygeneruje služby Key Vault `key1` a Zamění aktivní klíč z `key2` k `key1`. `key1` pak je označena jako aktivní klíč. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Vytvoření a generování tokenů

Můžete také požádat služby Key Vault ke generování tokenů sdílený přístupový podpis. Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Můžete udělit klientům přístup k prostředkům ve vašem účtu úložiště bez sdíleli své klíče účtu. Sdílený přístupový podpis poskytuje zabezpečený způsob, jak sdílet prostředky úložiště, aniž byste ohrozili své klíče účtu.

Příkazy v této části dokončit následující akce:

- Nastavte definici účet sdílený přístup podpis `<YourSASDefinitionName>`. Definice je nastavena na účet úložiště služby Key Vault spravovat `<YourStorageAccountName>` v trezoru klíčů `<VaultName>`.
- Vytvořte token účtu sdílený přístupový podpis pro objekt Blob, soubor, tabulky a fronty služby. Token, který je vytvořen pro typy prostředků, služby kontejneru a objektu. Token, který je vytvořen s všechna oprávnění, přes protokol https a se zadaným počátečním a koncovým datem.
- Nastavte podpis definici spravované služby Key Vault úložiště sdíleného přístupu v trezoru. Má tato definice šablony URI tokenu sdíleného přístupového podpisu, který byl vytvořen. Typ sdíleného přístupového podpisu má tato definice `account` a je platný po N dnech.
- Načíst skutečné přístupový token ze služby Key Vault tajný klíč, který odpovídá definici sdílený přístupový podpis.

Po dokončení kroků v předchozí části, spusťte následující příkazy požádat služby Key Vault ke generování tokenů sdílený přístupový podpis. 

1. Vytvoří definici sdílený přístupový podpis. Po vytvoření definice sdíleného přístupového podpisu, požádejte služby Key Vault ke generování více sdílených tokenů podpisu přístup. Tato operace vyžaduje `storage` a `setsas` oprávnění.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Nápovědu o operaci najdete v tématu [az úložiště účtu generovat sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) referenční dokumentaci.

    Po úspěšném spuštění operace kopírování výstupu.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Použití `$sasToken` vygenerované v předchozím příkazu a vytvořit definici sdílený přístupový podpis. Další informace o parametrech příkazového, najdete v článku [vytvoření az keyvault úložiště sas definice](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) referenční dokumentaci.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Pokud uživatel nemá oprávnění k účtu úložiště, nejprve Získejte ID objektu uživatele:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Načítání tokenů v kódu

Provádění operací na vašem účtu úložiště pomocí načítání [sdílených přístupových tokenů podpisu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) ze služby Key Vault.

Existují tři způsoby, jak ověření do služby Key Vault:

- Pomocí identity spravované služby. Tento přístup se důrazně doporučuje.
- Použijte instanční objekt a certifikát. 
- Použijte instanční objekt služby a heslo. Tento přístup nedoporučuje.

Další informace najdete v tématu [Azure Key Vault: Základní koncepty](key-vault-whatis.md#basic-concepts).

Následující příklad ukazuje, jak načíst sdílený přístupový podpis tokeny. Jakmile vytvoříte definici sdíleného přístupového podpisu se načíst tokeny. 

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

Pokud váš token sdíleného přístupového podpisu brzy vyprší platnost, znovu načíst změny token sdíleného přístupového podpisu ze služby Key Vault a aktualizujte kód.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Příkazy Azure CLI

Informace o příkazech rozhraní příkazového řádku Azure, které souvisí s účty spravované úložiště, najdete v článku [az keyvault úložiště](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) referenční dokumentaci.

## <a name="next-steps"></a>Další postup

- Další informace o [klíče, tajné kódy a certifikáty](https://docs.microsoft.com/rest/api/keyvault/).
- Projděte si články na [blog týmu Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
