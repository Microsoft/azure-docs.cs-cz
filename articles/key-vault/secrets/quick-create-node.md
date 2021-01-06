---
title: Rychlý Start-Azure Key Vault Klientská knihovna pro JavaScript (verze 4)
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny JavaScriptu.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: afb0e04d6f8a34d844df382081d53a32899e9a5c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934760"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Rychlý Start: Klientská knihovna Azure Key Vault tajných klíčů pro JavaScript (verze 4)

Začínáme s klientskou knihovnou Azure Key Vault tajných klíčů pro JavaScript [Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu se naučíte vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny JavaScriptu.

Prostředky klientské knihovny Key Vault:

[Referenční dokumentace k](/javascript/api/overview/azure/key-vault-index)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Další informace o Key Vault a tajných klíčích najdete v tématech:
- [Přehled Key Vault](../general/overview.md)
- [Přehled tajných klíčů](about-secrets.md)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aktuální [Node.js](https://nodejs.org) pro váš operační systém.
- [Azure CLI](/cli/azure/install-azure-cli)
- Key Vault – můžete ho vytvořit pomocí [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)nebo [Azure PowerShell](../general/quick-create-powershell.md)

V tomto rychlém startu se předpokládá, že používáte [Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Spusťte příkaz `login`.

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

    V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu.

2. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

## <a name="create-new-nodejs-application"></a>Vytvořit novou Node.js aplikaci

Pak vytvořte Node.js aplikaci, která se dá nasadit do cloudu. 

1. V příkazovém prostředí vytvořte složku s názvem `key-vault-node-app` :

```azurecli
mkdir key-vault-node-app
```

1. Přejděte do nově vytvořeného adresáře *trezoru klíčů-Node-App* a spusťte příkaz init pro inicializaci projektu Node:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Nainstalovat Key Vault balíčky

V okně konzoly nainstalujte Azure Key Vault [knihovny tajných](https://www.npmjs.com/package/@azure/keyvault-secrets) kódů pro Node.js.

```azurecli
npm install @azure/keyvault-secrets
```

Nainstalujte balíček [Azure. identity](https://www.npmjs.com/package/@azure/identity) , aby se ověřil na Key Vault.

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Tato aplikace používá název trezoru klíčů jako proměnnou prostředí s názvem `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS nebo Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění tajnosti vašemu uživatelskému účtu.

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Příklady kódu

Následující ukázka kódu vám ukáže, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč. 

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

1. Vytvořte nový textový soubor a uložte ho jako index.js.

1. Přidání vyžadování volání pro načtení modulů Azure a Node.js

1. Vytvoření struktury pro program, včetně základního zpracování výjimek

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z [knihovny Azure identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace o ověřování do trezoru klíčů najdete v tématu [Příručka pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Přidejte následující kód do funkce main ().

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí [metody setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?#setsecret-string--string--setsecretoptions-) , která vyžaduje název tajného kódu – v této ukázce používáme "mySecret".  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody getsecret](/javascript/api/@azure/keyvault-secrets/secretclient?#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Váš tajný kód se teď uloží jako `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec smažte a vyprázdnit tajný klíč z trezoru klíčů pomocí metod [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) a [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_) .

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Ukázka kódu

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testování a ověření

1. Spusťte následující příkazy ke spuštění aplikace.

    ```azurecli
    npm install
    npm index.js
    ```

1. Po zobrazení výzvy zadejte tajnou hodnotu. Například mySecretPassword.

    Zobrazí se varianta následujícího výstupu:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si [přehled Azure Key Vault tajných klíčů](about-secrets.md)
- Jak [zabezpečit přístup k trezoru klíčů](../general/secure-your-key-vault.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
