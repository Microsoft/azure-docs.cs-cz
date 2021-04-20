---
title: Rychlý Start – Azure Key Vault klíčovou knihovnou klienta pro JavaScript (verze 4)
description: Naučte se vytvářet, načítat a odstraňovat klíče z trezoru klíčů Azure pomocí klientské knihovny JavaScript.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: c0066409732f4492186ea0bf604261e1ab59ca9f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750297"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Rychlý Start: Azure Key Vault klíč klientské knihovny pro JavaScript (verze 4)

Začněte s knihovnou klienta Azure Key Vault Key pro JavaScript. [Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště pro kryptografické klíče. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu se naučíte vytvářet, načítat a odstraňovat klíče z trezoru klíčů Azure pomocí klientské knihovny JavaScript Key.

Prostředky klientské knihovny Key Vault:

[Referenční dokumentace k](/javascript/api/overview/azure/key-vault-index)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Další informace o Key Vault a klíčích najdete v tématech:
- [Přehled Key Vault](../general/overview.md)
- [Přehled klíčů](about-keys.md).

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

V okně konzoly nainstalujte [knihovnu Azure Key Vault Keys](https://www.npmjs.com/package/@azure/keyvault-keys) pro Node.js.

```azurecli
npm install @azure/keyvault-keys
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

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje klíčovým oprávněním k vašemu uživatelskému účtu.

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Příklady kódu

Následující ukázka kódu vám ukáže, jak vytvořit klienta, nastavit klíč, načíst klíč a odstranit klíč. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z [knihovny Azure identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace o ověřování do trezoru klíčů najdete v tématu [Příručka pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Přidejte následující kód do funkce main ().

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Uložení klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit klíč pomocí [metody createKey](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) . parametry metody akceptují název klíče a [typ klíče](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype) .

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Načtení klíče

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody getKey](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Odstranění klíče

Nakonec tento klíč odstraníme a vymažeme z vašeho trezoru klíčů pomocí metod [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) a [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_) .

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Ukázka kódu

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testování a ověření

Spusťte následující příkazy ke spuštění aplikace.

```azurecli
npm install
npm index.js
```

Zobrazí se varianta následujícího výstupu:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili klíč a načetli jste tento klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si [přehled Azure Key Vaultch klíčů](about-keys.md)
- Jak [zabezpečit přístup k trezoru klíčů](../general/security-overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
