---
title: Úvodní příručka – klientská knihovna Azure Key Vault pro Python
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny Pythonu
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 77cafc18528826ed90145e307f419c360b6a5e4b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79457198"
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

Z okna konzoly nainstalujte knihovnu tajných klíčů azure trezoru klíčů pro Python.

```console
pip install azure-keyvault-secrets
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

Nejjednodušší způsob ověření aplikace .NET na principu shluků je se spravovanou identitou. podrobnosti najdete [v tématu Použití spravované identity služby App Service pro přístup k azure key vault](managed-identity.md) pro podrobnosti. Z důvodu jednoduchosti však tento rychlý start vytvoří aplikaci konzoly .NET. Ověření desktopové aplikace pomocí Azure vyžaduje použití instančního objektu a zásad řízení přístupu.

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

Vytvořte zásady přístupu pro trezor klíčů, který uděluje oprávnění k instančnímu objektu klienta příkazu [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Udělte instančníobjekt get, list a nastavit oprávnění pro klíče i tajné klíče.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
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

Klientská knihovna Azure Key Vault pro Python umožňuje spravovat klíče a související prostředky, jako jsou certifikáty a tajné klíče. Ukázky kódu níže vám ukáže, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Celá konzolová aplikace https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appje k dispozici na adrese .

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidání směrnic

Na začátek kódu přidejte následující direktivy:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověření do trezoru klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí v kroku [Nastavit proměnné prostředí](#set-environmental-variables) výše. Název trezoru klíčů je rozšířen na identifikátor URI trezoru klíčů ve formátu "https://<>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Nyní, když je aplikace ověřena, můžete vložit tajný klíč do trezoru klíčů pomocí klienta. SetSecret metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) To vyžaduje název pro tajný klíč – používáme "mySecret" v této ukázce.  

```python
client.set_secret(secretName, secretValue)
```

Pomocí příkazu [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) můžete ověřit, zda byl tajný klíč nastaven:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného klíče

Nyní můžete načíst dříve nastavenou hodnotu s [klientem. GetSecret metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Vaše tajemství je `retrieved_secret.value`nyní uloženo jako .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec odstraníme tajný klíč z trezoru klíčů s [klientem. DeleteSecret metoda](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName)
```

Můžete ověřit, že tajný klíč je pryč s [az keyvault tajné show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) příkazu:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
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
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print("Your secret is '" + secretValue + "'.")

print("Retrieving your secret from " + keyVaultName + ".")

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.")
print("Deleting your secret from " + keyVaultName + " ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](key-vault-overview.md)
- Podívejte se na [průvodce vývojářem azure key vaultu](key-vault-developers-guide.md)
- Informace o [klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [doporučených postupů azure key vaultu](key-vault-best-practices.md)
