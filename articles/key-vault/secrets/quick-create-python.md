---
title: Rychlý Start – Azure Key Vault knihovna klienta Pythonu – Správa tajných klíčů
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny Pythonu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: f1f044eb3af35019eaf010e118bc4a5814269e9e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378542"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Rychlý Start: Klientská knihovna Azure Key Vault tajných klíčů pro Python

Začněte s knihovnou klienta Azure Key Vault pro Python. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Použití klientské knihovny Key Vault pro Python pro:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvářejte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky škálování cloudu a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte ověřený HSM úrovně 2 FIPS 140-2.

[Referenční dokumentace k](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 nebo novější
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/)

V tomto rychlém startu se předpokládá, že používáte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) v okně terminálu Linux.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-package"></a>Instalace balíčku

V okně konzoly nainstalujte Azure Key Vault knihovny tajných kódů pro Python.

```console
pip install azure-keyvault-secrets
```

Pro tento rychlý Start budete muset nainstalovat taky balíček Azure. identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Udělte instančnímu objektu přístup k vašemu trezoru klíčů.

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Nastavení proměnných prostředí

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro Python umožňuje správu klíčů a souvisejících prostředků, jako jsou certifikáty a tajné klíče. Následující ukázka kódu vám ukáže, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Ukázková aplikace, která ukazuje podobné operace jako v tomto článku, i další Key Vault funkce, jsou k dispozici na [GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets/samples).

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověřování pro váš Trezor klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí v kroku [nastavit proměnné prostředí](#set-environmental-variables) výše. Název trezoru klíčů se rozšíří na identifikátor URI trezoru klíčů ve formátu "https://<your-key-trezor-Name>. vault.azure.net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí klienta. [set_secret – metoda](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) To vyžaduje název tajného kódu – v této ukázce používáme "mySecret".  

```python
client.set_secret(secretName, secretValue)
```

Můžete ověřit, jestli je tajný kód nastavený pomocí příkazu [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Váš tajný kód se teď uloží jako `retrieved_secret.value` .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec pomocí [metody begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-)odstraníme tajný klíč z vašeho trezoru klíčů.

```python
client.begin_delete_secret(secretName)
```

V případě, že se tajný klíč nachází, můžete ověřit pomocí příkazu [AZ klíčů trezor tajné zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání trezoru klíčů a odpovídající skupiny prostředků použít Azure CLI nebo Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Ukázka kódu

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.begin_delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- [Přehled Azure Key Vault](../general/overview.md)
- [Azure Key Vault příručka pro vývojáře](../general/developers-guide.md)
- [Azure Key Vault osvědčené postupy](../general/best-practices.md)
