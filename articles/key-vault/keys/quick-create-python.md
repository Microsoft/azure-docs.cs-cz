---
title: Úvodní příručka – klientská knihovna Azure Key Vault pro Python
description: Naučte se vytvářet, načítat a odstraňovat klíče z trezoru klíčů Azure pomocí klientské knihovny Pythonu
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: e6120d5961dc31845c1322d052d46b52f4d2be6c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424178"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Úvodní příručka: Klientská knihovna Azure Key Vault pro Python

Začínáme s klientskou knihovnou Azure Key Vault pro Python. Podle následujících kroků nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Trezor klíčů pro Python:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvořte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky cloudovému škálování a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte fips 140-2 úroveň 2 ověřené hmeony.

[Referenční dokumentace](/python/api/overview/azure/key-vault?view=azure-python) | ROZHRANÍ[API Balíček zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [knihovny (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, 3.5.3 nebo novější
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview)

Tento rychlý start předpokládá, že používáte [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) v okně terminálu Linuxu.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-package"></a>Instalace balíčku

Z okna konzoly nainstalujte knihovnu klíčů Azure Key Vault pro Python.

```console
pip install azure-keyvault-keys
```

Pro tento rychlý start budete muset nainstalovat také balíček azure.identity:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

Tento rychlý start používá předem vytvořený trezor klíčů Azure. Trezor klíčů můžete vytvořit podle kroků v [rychlém startu Azure CLI](quick-create-cli.md), [rychlém startu Azure PowerShellu](quick-create-powershell.md)nebo [rychlém startu na webu Azure Portal](quick-create-portal.md). Případně můžete spustit příkazy Azure CLI níže.

> [!Important]
> Každý trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <> názvu trezoru s jedinečným klíčem.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nejjednodušší způsob ověření aplikace .NET na principu shluků je se spravovanou identitou. podrobnosti najdete [v tématu Použití spravované identity služby App Service pro přístup k azure key vault](../general/managed-identity.md) pro podrobnosti. Z důvodu jednoduchosti však tento rychlý start vytvoří aplikaci konzoly .NET. Ověření desktopové aplikace pomocí Azure vyžaduje použití instančního objektu a zásad řízení přístupu.

Vytvořte princip služby pomocí příkazu Azure CLI [az az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Tato operace vrátí řadu párů klíč / hodnota. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Poznamenejte si clientId a clientSecret, jak budeme používat v [Nastavit proměnné prostředí](#set-environmental-variables) krok níže.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Poskytnutí přístupu k objektu služeb trezoru klíčů

Vytvořte zásady přístupu pro trezor klíčů, který uděluje oprávnění k instančnímu objektu klienta příkazu [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Udělte instančnímu objektu oprávnění pro získání, seznam a vytvoření klíčů.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Nastavení proměnných prostředí

Metoda DefaultAzureCredential v naší aplikaci závisí na `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`třech `AZURE_TENANT_ID`proměnných prostředí: , , a . Nastavte tyto proměnné na clientId, clientSecret a clientId hodnoty, které jste `export VARNAME=VALUE` si poznamenali v Vytvořit krok [instančního objektu](#create-a-service-principal) pomocí formátu. (Tato metoda pouze nastaví proměnné pro aktuální prostředí a procesy vytvořené z prostředí; trvale `/etc/environment ` přidat tyto proměnné do vašeho prostředí, upravit soubor.) 

Název trezoru klíčů budete muset také uložit `KEY_VAULT_NAME`jako proměnnou prostředí nazvanou .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro Python umožňuje spravovat klíče a související prostředky, jako jsou certifikáty a tajné klíče. Ukázky kódu níže vám ukáže, jak vytvořit klienta, vytvořit klíč, načíst klíč a odstranit klíč.

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidání směrnic

Na začátek kódu přidejte následující direktivy:

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověření do trezoru klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí v kroku [Nastavit proměnné prostředí](#set-environmental-variables) výše. Název trezoru klíčů je rozšířen na identifikátor URI trezoru klíčů ve formátu "https://<>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Uložení klíče

Nyní, když je vaše aplikace ověřena, můžete vložit klíč do trezoru klíčů 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

Pomocí příkazu [az keyvault keyvault můžete](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) ověřit, zda byl klíč nastaven:

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Načtení klíče

Nyní můžete načíst dříve vytvořený klíč

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

Klíč je nyní `retrieved_key`uložen jako .

### <a name="delete-a-key"></a>Odstranění klíče

Nakonec odstraníme klíč z trezoru klíčů.

```python
client.delete_key(keyName)
```

Pomocí příkazu [az keyvault key-show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) můžete ověřit, zda je klíč pryč:

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít Azure CLI nebo Azure PowerShell odebrat trezor klíčů a odpovídající skupinu prostředků.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Ukázka kódu

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů, uložili klíč a načetli tento klíč. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](../general/overview.md)
- Podívejte se na [průvodce vývojářem azure key vaultu](../general/developers-guide.md)
- Kontrola [doporučených postupů azure key vaultu](../general/best-practices.md)
