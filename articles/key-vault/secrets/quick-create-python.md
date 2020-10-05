---
title: Rychlý Start – Azure Key Vault knihovna klienta Pythonu – Správa tajných klíčů
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny Pythonu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489200"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Rychlý Start: Klientská knihovna Azure Key Vault tajných klíčů pro Python

Začněte s knihovnou klienta Azure Key Vault pro Python. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Když použijete Key Vault k ukládání tajných klíčů, vyhnete se ukládání tajných kódů do kódu, což zvyšuje zabezpečení vaší aplikace.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Nastavení místního prostředí

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instalace Key Vault knihovny tajných klíčů:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Udělte instančnímu objektu přístup k vašemu trezoru klíčů.

Spusťte následující příkaz [AZ klíč-Policy set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) k autorizaci instančního objektu pro získání, výpis a nastavení operací na tajných klíčích. Tento příkaz spoléhá na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` proměnné prostředí a vytvořené v předchozích krocích.

# <a name="cmd"></a>[přepsat](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Tento příkaz spoléhá na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` proměnné prostředí a vytvořené v předchozích krocích.

Další informace najdete v tématu [přiřazení zásad přístupu – CLI](../general/assign-access-policy-cli.md) .

## <a name="create-the-sample-code"></a>Vytvoření ukázkového kódu

Klientská knihovna Azure Key Vault pro Python umožňuje správu tajných kódů a souvisejících prostředků, jako jsou certifikáty a kryptografické klíče. Následující příklad kódu ukazuje, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Vytvořte soubor s názvem *kv_secrets. py* , který obsahuje tento kód.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Spuštění kódu

Ujistěte se, že kód v předchozí části je v souboru s názvem *kv_secrets. py*. Pak spusťte kód pomocí následujícího příkazu:

```terminal
python kv_secrets.py
```

- Pokud dojde k chybám oprávnění, ujistěte se, že jste spustili [ `az keyvault set-policy` příkaz](#give-the-service-principal-access-to-your-key-vault).
- Opětovné spuštění kódu se stejným tajným názvem může způsobit chybu, tajný klíč "(konflikt) <name> je momentálně v odstraněném stavu, ale obnovený stav." Použijte jiný tajný název.

## <a name="code-details"></a>Podrobnosti o kódu

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V předchozím kódu [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objekt používá proměnné prostředí, které jste vytvořili pro objekt služby. Toto přihlašovací údaje zadáte vždy, když vytvoříte objekt klienta z knihovny Azure, například [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) , společně s identifikátorem URI prostředku, se kterým chcete přes tohoto klienta pracovat:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Po získání objektu klienta pro Trezor klíčů můžete tajný klíč uložit pomocí metody [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) : 

```python
client.set_secret(secretName, secretValue)
```

Volání `set_secret` generuje volání služby Azure REST API pro Trezor klíčů.

Při zpracování žádosti Azure ověřuje identitu volajícího (instanční objekt) pomocí objektu přihlašovacích údajů, který jste zadali klientovi.

Také kontroluje, zda je volající autorizován k provedení požadované akce. Tato autorizace k instančnímu objektu byla udělena dříve pomocí [ `az keyvault set-policy` příkazu](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Pro čtení tajného klíče z Key Vault použijte metodu [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) :

```python
retrieved_secret = client.get_secret(secretName)
 ```

Hodnota tajného klíče je obsažena v `retrieved_secret.value` .

Tajný klíč můžete také načíst pomocí příkazu Azure CLI [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Chcete-li odstranit tajný klíč, použijte metodu [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret`Metoda je asynchronní a vrací objekt cyklického dotazování. Volání metody cyklického dotazování `result` čeká na jeho dokončení.

Můžete ověřit, jestli byl tajný klíč odebraný pomocí příkazu Azure CLI [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

Po odstranění zůstane tajný kód v odstraněném stavu, ale po dobu. Pokud kód znovu spustíte, použijte jiný tajný název.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete také experimentovat s [certifikáty](../certificates/quick-create-python.md) a [klíči](../keys/quick-create-python.md), můžete znovu použít Key Vault vytvořená v tomto článku.

V opačném případě, až budete hotovi s prostředky vytvořenými v tomto článku, odstraňte skupinu prostředků a všechny její obsažené prostředky pomocí následujícího příkazu:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Key Vault](../general/overview.md)
- [Azure Key Vault příručka pro vývojáře](../general/developers-guide.md)
- [Azure Key Vault osvědčené postupy](../general/best-practices.md)
- [Ověřování pomocí Key Vault](../general/authentication.md)
