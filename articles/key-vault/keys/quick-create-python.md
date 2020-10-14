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
ms.openlocfilehash: 2eeb68ca5b0b6be0970a1adb071a7662399bc879
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042554"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Rychlý Start: Klientská knihovna pro Azure Key Vault klíče pro Python

Začněte s knihovnou klienta Azure Key Vault pro Python. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Když použijete Key Vault k ukládání kryptografických klíčů, vyhnete se ukládání takových klíčů v kódu, což zvyšuje zabezpečení vaší aplikace.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-keys-readme)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Požadované součásti

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + nebo 3.5.3 +](https://docs.microsoft.com/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

V tomto rychlém startu se předpokládá, že používáte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) v okně terminálu Linux.

## <a name="set-up-your-local-environment"></a>Nastavení místního prostředí

Tento rychlý Start používá Azure identity Library s Azure CLI k ověřování uživatele ve službách Azure. Vývojáři můžou k ověřování hovorů použít taky Visual Studio nebo Visual Studio Code, další informace najdete v tématu [ověření klienta pomocí klientské knihovny Azure identity](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Spusťte příkaz `login`.

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

    V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu.

2. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

### <a name="install-the-packages"></a>Nainstalovat balíčky

1. V terminálu nebo příkazovém řádku vytvořte vhodnou složku projektu a pak vytvořte a aktivujte virtuální prostředí Pythonu, jak je popsáno v tématu [použití virtuálních prostředí Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Nainstalujte Azure Active Directory knihovnu identit:

    ```terminal
    pip install azure.identity
    ```


1. Nainstalujte klientskou knihovnu Key Vault Key:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění tajného kódu vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Tato aplikace používá název trezoru klíčů jako proměnnou prostředí s názvem `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS nebo Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Vytvoření ukázkového kódu

Klientská knihovna Azure Key Vault Key pro Python umožňuje spravovat kryptografické klíče. Následující příklad kódu ukazuje, jak vytvořit klienta, nastavit klíč, načíst klíč a odstranit klíč.

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

- Pokud dojde k chybám oprávnění, ujistěte se, že jste spustili [ `az keyvault set-policy` příkaz](#grant-access-to-your-key-vault).
- Opětovné spuštění kódu se stejným názvem klíče může způsobit chybu, klíč "(konflikt) <name> je momentálně v odstraněném stavu, ale obnovený stav." Použijte jiný název klíče.

## <a name="code-details"></a>Podrobnosti o kódu

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu  [' DefaultAzureCredential () '](/python/api/azure-identity/azure.identity.defaultazurecredential) , která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace najdete v tématu [výchozí ověřování pověření Azure](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Uložení klíče

Po získání objektu klienta pro Trezor klíčů můžete klíč uložit pomocí metody [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) : 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Můžete také použít [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) nebo [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-).

Volání `create` metody vygeneruje volání služby Azure REST API pro Trezor klíčů.

Při zpracování žádosti Azure ověřuje identitu volajícího (instanční objekt) pomocí objektu přihlašovacích údajů, který jste zadali klientovi.

## <a name="retrieve-a-key"></a>Načtení klíče

Chcete-li číst klíč z Key Vault, použijte metodu [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) :

```python
retrieved_key = client.get_key(keyName)
 ```

Můžete také ověřit, že byl klíč nastaven pomocí příkazu Azure CLI [AZ Key trezor Key show](/cli/azure/keyvault/key?#az-keyvault-key-show).

### <a name="delete-a-key"></a>Odstranění klíče

Chcete-li odstranit klíč, použijte metodu [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) :

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key`Metoda je asynchronní a vrací objekt cyklického dotazování. Volání metody cyklického dotazování `result` čeká na jeho dokončení.

Můžete ověřit, jestli se klíč odstranil pomocí příkazu Azure CLI [AZ Key trezor Key show](/cli/azure/keyvault/key?#az-keyvault-key-show).

Po odstranění klíč zůstane v odstraněném stavu, ale po dobu se obnoví. Pokud kód znovu spustíte, použijte jiný název klíče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete také experimentovat s [certifikáty](../certificates/quick-create-python.md) a [tajnými](../secrets/quick-create-python.md)kódy, můžete znovu použít Key Vault vytvořená v tomto článku.

V opačném případě, až budete hotovi s prostředky vytvořenými v tomto článku, odstraňte skupinu prostředků a všechny její obsažené prostředky pomocí následujícího příkazu:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Key Vault](../general/overview.md)
- [Zabezpečený přístup k trezoru klíčů](../general/secure-your-key-vault.md)
- [Azure Key Vault příručka pro vývojáře](../general/developers-guide.md)
- [Azure Key Vault osvědčené postupy](../general/best-practices.md)
- [Ověřování pomocí Key Vault](../general/authentication.md)
