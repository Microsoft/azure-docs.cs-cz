---
title: Správa klíčů účtů úložiště pomocí azure key vaultu a azure cli
description: Klíče účtů úložiště poskytují bezproblémovou integraci mezi Azure Key Vault a přístupem na základě klíčů k účtu úložiště Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 0b855584ef6efef574e8264f3cead79000a51b13
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432005"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Správa klíčů účtů úložiště pomocí trezoru klíčů a nastavení příkazového příkazového příkazu Azure

Účet úložiště Azure používá přihlašovací údaje obsahující název účtu a klíč. Klíč je automaticky generován a slouží jako heslo, nikoli jako kryptografický klíč. Trezor klíčů spravuje klíče účtů úložiště jejich uložením jako tajné klíče trezoru klíčů. 

Pomocí funkce klíče spravovaného úložiště spravovaného úložiště úložiště key vault upsat (synchronizovat) klíče s účtem úložiště Azure a pravidelně je regenerovat (otáčet). Klíče můžete spravovat jak pro účty úložiště, tak pro klasické účty úložiště.

Při použití funkce klíče účtu spravovaného úložiště zvažte následující body:

- Hodnoty klíče jsou nikdy vráceny v reakci na volajícího.
- Klíče účtu úložiště by měl spravovat pouze trezor klíčů. Nespravujte klíče sami a nezasahujte do procesů trezoru klíčů.
- Klíče účtu úložiště by měl spravovat pouze jeden objekt trezoru klíčů. Nepovolujte správu klíčů z více objektů.
- Trezor klíčů můžete požádat o správu účtu úložiště pomocí uživatelského objektu, ale ne pomocí instančního objektu.
- Obnovte klíče pouze pomocí trezoru klíčů. Neobnovujte ručně klíče účtu úložiště.

Doporučujeme používat integraci Azure Storage s Azure Active Directory (Azure AD), cloudovou službou správy identit a přístupu Microsoftu. Integrace Azure AD je dostupná pro [objekty BLOB azure a fronty](../../storage/common/storage-auth-aad.md)a poskytuje přístup k Azure Storage založený na tokenech OAuth2 (stejně jako Azure Key Vault).

Azure AD umožňuje ověřit klientskou aplikaci pomocí aplikace nebo identity uživatele, namísto přihlašovacích údajů účtu úložiště. Identitu [spravanou azure ad](/azure/active-directory/managed-identities-azure-resources/) můžete použít při spuštění v Azure. Spravované identity odeberou potřebu ověřování klientů a ukládání přihlašovacích údajů v aplikaci nebo s ní.

Azure AD používá řízení přístupu na základě rolí (RBAC) ke správě autorizace, která je také podporována trezoru klíčů.

## <a name="service-principal-application-id"></a>ID aplikace instančního objektu

Klient Azure AD poskytuje každé registrované aplikaci [s instančním objektem](/azure/active-directory/develop/developer-glossary#service-principal-object). Instanční objekt slouží jako ID aplikace, které se používá při nastavení autorizace pro přístup k jiným prostředkům Azure prostřednictvím RBAC.

Trezor klíčů je aplikace Microsoftu, která je předem registrovaná ve všech klientech Azure AD. Trezor klíčů se zaeviduje pod stejným ID aplikace v každém cloudu Azure.

| Tenanti | Cloud | ID aplikace |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Veřejný Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Ostatní  | Všechny | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Požadavky

Chcete-li tuto příručku dokončit, musíte nejprve provést následující kroky:

- [Nainstalujte příkazové příkazové nebo tonové zaokrocené.](/cli/azure/install-azure-cli)
- [Vytvoření trezoru klíčů](quick-create-cli.md)
- [Vytvořte účet úložiště Azure](../../storage/common/storage-account-create.md?tabs=azure-cli). Název účtu úložiště musí používat pouze malá písmena a čísla. Délka názvu musí být mezi 3 a 24 znaky.
      
## <a name="manage-storage-account-keys"></a>Správa klíčů účtu úložiště

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Ověřte relaci Azure CLI pomocí příkazů [az přihlášení.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Poznamte trezoru klíčů přístup k účtu úložiště

Pomocí příkazu vytvoření role Azure CLI [az](/cli/azure/role/assignment?view=azure-cli-latest) můžete udělit přístup k úložišti úložiště. Zadejte příkaz následující hodnoty parametrů:

- `--role`: Předaj roli RBAC "Role operátora klíče klienta úložiště". Tato role omezuje rozsah přístupu na váš účet úložiště. Pro klasický účet úložiště, předat "Classic Account Key Key Service Role" místo.
- `--assignee-object-id`: Předajte hodnotu "93c27d83-f79b-4cb2-8dd4-4aa716542e74", což je ID objektu pro trezor klíčů ve veřejném cloudu Azure. (Pokud chcete získat ID objektu pro trezor klíčů v cloudu Azure Government, přečtěte si informace [o ID hlavního povinného servisu](#service-principal-application-id).)
- `--scope`: Předejte ID prostředku účtu úložiště, `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`které je ve formuláři . Pokud chcete najít ID předplatného, použijte příkaz [az az](/cli/azure/account?view=azure-cli-latest#az-account-list) Azure CLI. chcete-li najít název účtu úložiště a skupinu prostředků účtu úložiště, použijte příkaz [seznamu az účtu az](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) Azure CLI.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Create a Key Vault Managed storage account

 Vytvořte účet spravovaného úložiště úložiště trezoru klíčů pomocí příkazu úložiště Azure CLI [az keyvault.](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) Nastavte regenerační dobu 90 dnů. Po 90 dnech trezor key `key1` regeneruje a zamění aktivní klíč z `key2` aplikace `key1`. `key1`je pak označen jako aktivní klíč. Zadejte příkaz následující hodnoty parametrů:

- `--vault-name`: Podejte název trezoru klíčů. Chcete-li najít název trezoru klíčů, použijte příkaz [az keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) Azure CLI.
- `-n`: Předejte název svého účtu úložiště. Pokud chcete najít název vašeho účtu úložiště, použijte příkaz [az úložiště](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) Azure CLI.
- `--resource-id`: Předejte ID prostředku účtu úložiště, `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`které je ve formuláři . Pokud chcete najít ID předplatného, použijte příkaz [az az](/cli/azure/account?view=azure-cli-latest#az-account-list) Azure CLI. chcete-li najít název účtu úložiště a skupinu prostředků účtu úložiště, použijte příkaz [seznamu az účtu az](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) Azure CLI.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokeny sdílených přístupových podpisů

Trezor klíčů můžete také požádat o vygenerování tokenů podpisu sdíleného přístupu. Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům v účtu úložiště. Klientům můžete udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. Sdílený přístupový podpis poskytuje bezpečný způsob sdílení prostředků úložiště bez ohrožení klíčů účtu.

Příkazy v této části dokončí následující akce:

- Nastavte definici podpisu `<YourSASDefinitionName>`sdíleného přístupu účtu . The definition is set on a Key Vault managed storage account `<YourStorageAccountName>` in your key vault `<YourKeyVaultName>`.
- Vytvořte token podpisu sdíleného přístupu účtu pro služby blob, soubor, tabulka a fronty. Token je vytvořen pro typy prostředků Service, Container a Object. Token je vytvořen se všemi oprávněními, přes https a se zadaným počátečním a koncovým datem.
- Nastavte v trezoru definici sdíleného přístupového podpisu spravovaného úložiště úložiště klíčů. Definice obsahuje šablonu URI tokenu sdíleného přístupového podpisu, který byl vytvořen. Definice má typ sdíleného `account` přístupového podpisu a je platná pro N dní.
- Ověřte, zda byl sdílený přístupový podpis uložen v trezoru klíčů jako tajný klíč.

### <a name="create-a-shared-access-signature-token"></a>Vytvoření tokenu sdíleného přístupového podpisu

Vytvořte definici podpisu sdíleného přístupu pomocí příkazu Azure CLI [az storage account generate-sas.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Tato operace `storage` vyžaduje `setsas` oprávnění a.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Po úspěšném spuštění operace zkopírujte výstup.

```console
"se=2020-01-01&sp=***"
```

Tento výstup bude předán `--template-id` parametru v dalším kroku.

### <a name="generate-a-shared-access-signature-definition"></a>Generování definice sdíleného přístupového podpisu

Pomocí příkazu vytvořit azure CLI [az keyvault úložiště sas definice,](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) předání `--template-id` výstupu z předchozího kroku na parametr, k vytvoření definice podpisu sdíleného přístupu.  Můžete zadat název dle vašeho `-n` výběru parametru.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Ověření definice sdíleného přístupového podpisu

Můžete ověřit, že definice sdíleného přístupového podpisu byla uložena ve vašem trezoru klíčů pomocí seznamu tajných adres Azure CLI [az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) a tajných [příkazů az keyvault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

Nejprve vyhledejte definici sdíleného přístupového podpisu v trezoru klíčů pomocí příkazu [az keyvault secret list.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Tajný klíč odpovídající definici SAS bude mít tyto vlastnosti:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Nyní můžete použít příkaz [az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) `id` secret show a vlastnost k zobrazení obsahu tohoto tajného klíče.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Výstup tohoto příkazu zobrazí řetězec definice`value`SAS jako .


## <a name="next-steps"></a>Další kroky

- Další informace o [klíčích, tajných kódech a certifikátech](https://docs.microsoft.com/rest/api/keyvault/).
- Projděte si články na [blogu týmu Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Podívejte se na referenční dokumentaci [k úložišti az keyvault.](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)
