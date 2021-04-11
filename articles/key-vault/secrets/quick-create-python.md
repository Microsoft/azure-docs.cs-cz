---
title: Rychlý Start – Azure Key Vault knihovna klienta Pythonu – Správa tajných klíčů
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny Pythonu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b82c86983bc07f39c1adb5aa741497d8cc3246e9
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967115"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Rychlý Start: Azure Key Vaulta klíčová knihovna klienta pro Python

Začínáme s klientskou knihovnou Azure Key Vault tajných klíčů pro Python Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Když použijete Key Vault k ukládání tajných klíčů, vyhnete se ukládání tajných kódů do kódu, což zvyšuje zabezpečení vaší aplikace.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-secrets-readme)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + nebo 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

V tomto rychlém startu se předpokládá, že používáte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) v okně terminálu Linux.


## <a name="set-up-your-local-environment"></a>Nastavení místního prostředí
Tento rychlý Start používá Azure identity Library s Azure CLI k ověřování uživatele ve službách Azure. Vývojáři můžou k ověřování hovorů použít taky Visual Studio nebo Visual Studio Code, další informace najdete v tématu [ověření klienta pomocí klientské knihovny Azure identity](/python/api/overview/azure/identity-readme).

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
    pip install azure-identity
    ```


1. Instalace Key Vault knihovny tajných klíčů:

    ```terminal
    pip install azure-keyvault-secrets
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

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Vytvoření ukázkového kódu

Klientská knihovna Azure Key Vault tajných klíčů pro Python umožňuje správu tajných kódů. Následující příklad kódu ukazuje, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

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

- Pokud dojde k chybám oprávnění, ujistěte se, že jste spustili [ `az keyvault set-policy` příkaz](#grant-access-to-your-key-vault).
- Opětovné spuštění kódu se stejným tajným názvem může způsobit chybu, tajný klíč "(konflikt) <name> je momentálně v odstraněném stavu, ale obnovený stav." Použijte jiný tajný název.

## <a name="code-details"></a>Podrobnosti o kódu

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu  [' DefaultAzureCredential () '](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) , která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace najdete v tématu [výchozí ověřování pověření Azure](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Po získání objektu klienta pro Trezor klíčů můžete tajný klíč uložit pomocí metody [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) : 

```python
client.set_secret(secretName, secretValue)
```

Volání `set_secret` generuje volání služby Azure REST API pro Trezor klíčů.

Při zpracování žádosti Azure ověřuje identitu volajícího (instanční objekt) pomocí objektu přihlašovacích údajů, který jste zadali klientovi.

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Pro čtení tajného klíče z Key Vault použijte metodu [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) :

```python
retrieved_secret = client.get_secret(secretName)
 ```

Hodnota tajného klíče je obsažena v `retrieved_secret.value` .

Tajný klíč můžete také načíst pomocí příkazu Azure CLI [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Chcete-li odstranit tajný klíč, použijte metodu [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret`Metoda je asynchronní a vrací objekt cyklického dotazování. Volání metody cyklického dotazování `result` čeká na jeho dokončení.

Můžete ověřit, jestli byl tajný klíč odebraný pomocí příkazu Azure CLI [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

Po odstranění zůstane tajný kód v odstraněném stavu, ale po dobu. Pokud kód znovu spustíte, použijte jiný tajný název.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete také experimentovat s [certifikáty](../certificates/quick-create-python.md) a [klíči](../keys/quick-create-python.md), můžete znovu použít Key Vault vytvořená v tomto článku.

V opačném případě, až budete hotovi s prostředky vytvořenými v tomto článku, odstraňte skupinu prostředků a všechny její obsažené prostředky pomocí následujícího příkazu:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Další kroky

- [Přehled Azure Key Vault](../general/overview.md)
- [Zabezpečení přístupu k trezoru klíčů](../general/secure-your-key-vault.md)
- [Azure Key Vault příručka pro vývojáře](../general/developers-guide.md)
- [Přehled zabezpečení Key Vault](../general/security-overview.md)
- [Ověřování pomocí Key Vault](../general/authentication.md)
