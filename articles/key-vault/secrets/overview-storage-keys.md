---
title: Správa klíčů účtu úložiště pomocí Azure Key Vault a Azure CLI
description: Klíče účtu úložiště umožňují bezproblémovou integraci mezi Azure Key Vault a klíčovým přístupem k účtu služby Azure Storage.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e89716d0560cbf7960cb7bde67156c8df0045a31
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499216"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Správa klíčů účtu úložiště pomocí Key Vault a Azure CLI
> [!IMPORTANT]
> Doporučujeme používat Azure Storage integraci s Azure Active Directory (Azure AD), což je cloudová služba pro správu identit a přístupu od Microsoftu. Integrace Azure AD je k dispozici pro [objekty BLOB a fronty Azure](../../storage/common/storage-auth-aad.md)a poskytuje přístup založený na tokenech OAuth2 k Azure Storage (stejně jako Azure Key Vault). Azure AD umožňuje ověřování klientské aplikace pomocí identity aplikace nebo uživatele místo přihlašovacích údajů k účtu úložiště. Při spuštění v Azure můžete použít [spravovanou identitu Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) . Spravované identity odstraňují nutnost ověřování klientů a ukládání přihlašovacích údajů do aplikace nebo s vaší aplikací. Použijte toto řešení pouze v případě, že není možné ověřování Azure AD.

Účet úložiště Azure používá přihlašovací údaje, které zahrnují název účtu a klíč. Klíč se vygeneruje automaticky a slouží jako heslo, nikoli jako kryptografický klíč. Key Vault spravuje klíče účtu úložiště tím, že je pravidelně znovu generuje v účtu úložiště, a poskytuje tokeny sdíleného přístupového podpisu pro delegovaný přístup k prostředkům ve vašem účtu úložiště.

Pomocí funkce klíče spravovaného účtu úložiště Key Vault můžete zobrazit (synchronizovat) klíče s účtem služby Azure Storage a pravidelně je znovu vygenerovat (otočit) klíče. Klíče můžete spravovat jak pro účty úložiště, tak pro účty klasického úložiště.

Když použijete funkci klíče spravovaného účtu úložiště, vezměte v úvahu následující body:

- Hodnoty klíče se nikdy nevrátí jako odpověď volajícímu.
- Klíče účtu úložiště by měly spravovat jenom Key Vault. Nespravujte klíče sami a zabraňte v narušování Key Vaultch procesů.
- Klíče účtu úložiště by měl spravovat jenom jeden objekt Key Vault. Nepovoluje správu klíčů z více objektů.
- Obnovte klíče jenom pomocí Key Vault. Neobnovujte ručně klíče účtu úložiště.

## <a name="service-principal-application-id"></a>ID aplikace instančního objektu

Tenant služby Azure AD poskytuje každou registrovanou aplikaci s [instančním objektem](../../active-directory/develop/developer-glossary.md#service-principal-object). Instanční objekt slouží jako ID aplikace, které se během autorizačního nastavení používá pro přístup k jiným prostředkům Azure prostřednictvím Azure RBAC.

Key Vault je aplikace Microsoftu, která je předem registrovaná ve všech klientech Azure AD. Key Vault je zaregistrované pod stejným ID aplikace v každém cloudu Azure.

| Tenanti | Cloud | ID aplikace |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Veřejný Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Jiné  | Všechny | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tuto příručku, je třeba nejprve provést následující akce:

- [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).
- [Vytvořte trezor klíčů.](quick-create-cli.md)
- [Vytvořte účet úložiště Azure](../../storage/common/storage-account-create.md?tabs=azure-cli). Název účtu úložiště musí obsahovat jenom malá písmena a číslice. Název musí mít délku 3 až 24 znaků.
      
## <a name="manage-storage-account-keys"></a>Správa klíčů účtu úložiště

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Pomocí příkazů [AZ Login](/powershell/module/az.accounts/connect-azaccount) ověřte relaci Azure CLI.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Zadejte Key Vault přístup k vašemu účtu úložiště.

Pomocí příkazu Azure CLI [AZ role Assignment Create](/cli/azure/role/assignment) udělte Key Vault přístup k vašemu účtu úložiště. Zadejte příkaz pro následující hodnoty parametrů:

- `--role`: Předejte roli Azure role služby operátora klíče účtu úložiště. Tato role omezuje rozsah přístupu na váš účet úložiště. Pro klasický účet úložiště dejte místo toho roli služby operátora klíč účtu klasického úložiště.
- `--assignee`: Předejte hodnotu https://vault.azure.net , což je adresa URL pro Key Vault ve veřejném cloudu Azure. (V případě cloudového použití Azure Goverment použijte--asingee-Object-ID ', přečtěte si téma [ID aplikace instančního objektu](#service-principal-application-id).)
- `--scope`: Předejte své ID prostředku účtu úložiště, který je ve formuláři `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . ID předplatného zjistíte pomocí příkazu Azure CLI [AZ Account list](/cli/azure/account?#az-account-list) . Pokud chcete najít název účtu úložiště a skupinu prostředků účtu úložiště, použijte příkaz Azure CLI [AZ Storage Account list](/cli/azure/storage/account?#az-storage-account-list) .

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Poskytnutí oprávnění ke spravovaným účtům úložiště vašemu uživatelskému účtu

Pomocí Azure CLI [AZ klíčů trezor-set-Policy](/cli/azure/keyvault?#az-keyvault-set-policy) aktualizujte zásady Key Vault přístupu a udělte účtu úložiště oprávnění k vašemu uživatelskému účtu.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Všimněte si, že oprávnění k účtům úložiště nejsou k dispozici na stránce zásady přístupu na účtu úložiště v Azure Portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Vytvoření spravovaného účtu úložiště Key Vault

 Pomocí příkazu Azure CLI [AZ klíčů Storage](/cli/azure/keyvault/storage?#az-keyvault-storage-add) vytvořte účet spravovaného úložiště Key Vault. Nastavte dobu regenerace 90 dní. Když je čas na otočení, Trezor klíčů znovu vygeneruje klíč, který není aktivní, a pak nastaví nově vytvořený klíč jako aktivní. Pouze jeden z klíčů slouží k vystavování tokenů SAS v jednom okamžiku, jedná se o aktivní klíč. Zadejte příkaz pro následující hodnoty parametrů:

- `--vault-name`: Předejte název vašeho trezoru klíčů. Název vašeho trezoru klíčů zjistíte pomocí příkazu Azure CLI AZ Key [trezor list](/cli/azure/keyvault?#az-keyvault-list) .
- `-n`: Předejte název svého účtu úložiště. Název svého účtu úložiště zjistíte pomocí příkazu Azure CLI [AZ Storage Account list](/cli/azure/storage/account?#az-storage-account-list) .
- `--resource-id`: Předejte své ID prostředku účtu úložiště, který je ve formuláři `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . ID předplatného zjistíte pomocí příkazu Azure CLI [AZ Account list](/cli/azure/account?#az-account-list) . Pokud chcete najít název účtu úložiště a skupinu prostředků účtu úložiště, použijte příkaz Azure CLI [AZ Storage Account list](/cli/azure/storage/account?#az-storage-account-list) .
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokeny sdíleného přístupového podpisu

Můžete také požádat Key Vault o generování tokenů sdíleného přístupového podpisu. Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Klientům můžete udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. Sdílený přístupový podpis poskytuje zabezpečený způsob, jak sdílet prostředky úložiště bez narušení klíčů účtu.

Příkazy v této části dokončí následující akce:

- Nastavte definici sdíleného přístupového podpisu účtu `<YourSASDefinitionName>` . Definice je nastavená na spravovaném účtu úložiště Key Vault `<YourStorageAccountName>` ve vašem trezoru klíčů `<YourKeyVaultName>` .
- Vytvořte token sdíleného přístupového podpisu účtu pro služby blob, File, Table a Queue. Token se vytvoří pro službu typů prostředků, kontejner a objekt. Token se vytvoří se všemi oprávněními přes HTTPS a zadaným počátečním a koncovým datem.
- V trezoru nastavte Key Vault definici sdíleného přístupového podpisu spravovaného úložiště. Definice obsahuje identifikátor URI šablony tokenu sdíleného přístupového podpisu, který byl vytvořen. Definice má typ sdíleného přístupového podpisu `account` a je platná po dobu N dní.
- Ověřte, že se sdílený přístupový podpis uložil do trezoru klíčů jako tajný kód.

### <a name="create-a-shared-access-signature-token"></a>Vytvoření tokenu sdíleného přístupového podpisu

Pomocí příkazu Azure CLI [AZ Storage Account Generate-SAS](/cli/azure/storage/account?#az-storage-account-generate-sas) vytvořte definici sdíleného přístupového podpisu. Tato operace vyžaduje `storage` oprávnění a `setsas` .


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Po úspěšném spuštění operace zkopírujte výstup.

```console
"se=2020-01-01&sp=***"
```

Tento výstup bude předán `--template-uri` parametru v dalším kroku.

### <a name="generate-a-shared-access-signature-definition"></a>Generování definice sdíleného přístupového podpisu

Pomocí příkazu Azure CLI [AZ klíčů Storage SAS-definition Create](/cli/azure/keyvault/storage/sas-definition?#az-keyvault-storage-sas-definition-create) a předáním výstupu z předchozího kroku do `--template-uri` parametru vytvořte definici sdíleného přístupového podpisu.  Můžete zadat název svého výběru do `-n` parametru.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Ověření definice sdíleného přístupového podpisu

Můžete ověřit, jestli je definice sdíleného přístupového podpisu v trezoru klíčů uložená pomocí příkazu Azure CLI [AZ Key trezor Storage SAS-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) .

Nyní můžete použít příkaz [AZ klíčů Storage SAS-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) a `id` vlastnost k zobrazení obsahu tohoto tajného klíče.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [klíčích, tajných klíčích a certifikátech](/rest/api/keyvault/).
- Projděte si články na [blogu Azure Key Vault týmu](/archive/blogs/kv/).
- Viz Referenční dokumentace k [úložišti klíčů AZ klíčů trezor](/cli/azure/keyvault/storage) .
