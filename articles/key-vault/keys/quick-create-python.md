---
title: Rychlý Start – Azure Key Vault knihovna klienta Pythonu – Správa klíčů
description: Naučte se vytvářet, načítat a odstraňovat klíče z trezoru klíčů Azure pomocí klientské knihovny Pythonu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482115"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Rychlý Start: Klientská knihovna pro Azure Key Vault klíče pro Python

Začněte s knihovnou klienta Azure Key Vault pro Python. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Když použijete Key Vault k ukládání kryptografických klíčů, vyhnete se ukládání takových klíčů v kódu, což zvyšuje zabezpečení vaší aplikace.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Nastavení místního prostředí

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Instalace knihovny klíčů Key Vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Udělte instančnímu objektu přístup k vašemu trezoru klíčů.

Spusťte následující příkaz [AZ Key trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) k autorizaci instančního objektu pro operace odstranění, získání, vypsání a vytvoření na klíčích. 

# <a name="cmd"></a>[přepsat](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Tento příkaz spoléhá na `KEY_VAULT_NAME` `AZURE_CLIENT_ID` proměnné prostředí a vytvořené v předchozích krocích.

Další informace najdete v tématu [přiřazení zásad přístupu – CLI](../general/assign-access-policy-cli.md) .

## <a name="create-the-sample-code"></a>Vytvoření ukázkového kódu

Klientská knihovna Azure Key Vault pro Python umožňuje spravovat kryptografické klíče a související prostředky, jako jsou certifikáty a tajné klíče. Následující příklad kódu ukazuje, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Vytvořte soubor s názvem *kv_keys. py* , který obsahuje tento kód.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Spuštění kódu

Ujistěte se, že kód v předchozí části je v souboru s názvem *kv_keys. py*. Pak spusťte kód pomocí následujícího příkazu:

```terminal
python kv_keys.py
```

- Pokud dojde k chybám oprávnění, ujistěte se, že jste spustili [ `az keyvault set-policy` příkaz](#give-the-service-principal-access-to-your-key-vault).
- Opětovné spuštění kódu se stejným názvem klíče může způsobit chybu, klíč "(konflikt) <name> je momentálně v odstraněném stavu, ale obnovený stav." Použijte jiný název klíče.

## <a name="code-details"></a>Podrobnosti o kódu

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V předchozím kódu [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objekt používá proměnné prostředí, které jste vytvořili pro objekt služby. Toto přihlašovací údaje zadáte vždy, když vytvoříte objekt klienta z knihovny Azure, například [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python) , společně s identifikátorem URI prostředku, se kterým chcete přes tohoto klienta pracovat:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Uložení klíče

Po získání objektu klienta pro Trezor klíčů můžete klíč uložit pomocí metody [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) : 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Můžete také použít [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) nebo [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

Volání `create` metody vygeneruje volání služby Azure REST API pro Trezor klíčů.

Při zpracování žádosti Azure ověřuje identitu volajícího (instanční objekt) pomocí objektu přihlašovacích údajů, který jste zadali klientovi.

Také kontroluje, zda je volající autorizován k provedení požadované akce. Tato autorizace k instančnímu objektu byla udělena dříve pomocí [ `az keyvault set-policy` příkazu](#give-the-service-principal-access-to-your-key-vault).

## <a name="retrieve-a-key"></a>Načtení klíče

Chcete-li číst klíč z Key Vault, použijte metodu [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-) :

```python
retrieved_key = client.get_key(keyName)
 ```

Můžete také ověřit, že byl klíč nastaven pomocí příkazu Azure CLI [AZ Key trezor Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

### <a name="delete-a-key"></a>Odstranění klíče

Chcete-li odstranit klíč, použijte metodu [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-) :

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key`Metoda je asynchronní a vrací objekt cyklického dotazování. Volání metody cyklického dotazování `result` čeká na jeho dokončení.

Můžete ověřit, jestli se klíč odstranil pomocí příkazu Azure CLI [AZ Key trezor Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

Po odstranění klíč zůstane v odstraněném stavu, ale po dobu se obnoví. Pokud kód znovu spustíte, použijte jiný název klíče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete také experimentovat s [certifikáty](../certificates/quick-create-python.md) a [tajnými](../secrets/quick-create-python.md)kódy, můžete znovu použít Key Vault vytvořená v tomto článku.

V opačném případě, až budete hotovi s prostředky vytvořenými v tomto článku, odstraňte skupinu prostředků a všechny její obsažené prostředky pomocí následujícího příkazu:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Key Vault](../general/overview.md)
- [Azure Key Vault příručka pro vývojáře](../general/developers-guide.md)
- [Azure Key Vault osvědčené postupy](../general/best-practices.md)
- [Ověřování pomocí Key Vault](../general/authentication.md)
