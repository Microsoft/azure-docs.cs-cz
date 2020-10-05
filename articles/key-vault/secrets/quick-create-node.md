---
title: Rychlý Start – Azure Key Vault klientskou knihovnu pro Node.js (v4)
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny Node.js.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 96826cbd7ea021f3596b3f92a484a05089aa9318
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91336662"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Rychlý Start: Azure Key Vault klientskou knihovnu pro Node.js (v4)

Začínáme s klientskou knihovnou Azure Key Vault pro Node.js Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Key Vault Node.js:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvářejte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky škálování cloudu a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte ověřený HSM úrovně 2 FIPS 140-2.

[Referenční dokumentace k](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aktuální [Node.js](https://nodejs.org) pro váš operační systém.
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/)

V tomto rychlém startu se předpokládá, že používáte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) v okně terminálu Linux.

## <a name="setting-up"></a>Nastavení

### <a name="install-the-package"></a>Instalace balíčku

V okně konzoly nainstalujte Azure Key Vault knihovny tajných kódů pro Node.js.

```console
npm install @azure/keyvault-secrets
```

Pro tento rychlý Start budete muset nainstalovat taky balíček Azure. identity:

```console
npm install @azure/identity
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

Klientská knihovna Azure Key Vault pro Node.js umožňuje správu klíčů a souvisejících prostředků, jako jsou certifikáty a tajné klíče. Následující ukázka kódu vám ukáže, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Celá Konzolová aplikace je k dispozici na adrese https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověřování pro váš Trezor klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí z kroku [nastavit proměnné prostředí](#set-environmental-variables) a [konstruktoru SecretClient](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

Název trezoru klíčů se rozšíří na identifikátor URI trezoru klíčů ve formátu `https://<your-key-vault-name>.vault.azure.net` . 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí [metody Client. setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-) , která vyžaduje název tajného kódu – v této ukázce používáme "mySecret".  

```javascript
await client.setSecret(secretName, secretValue);
```

Můžete ověřit, jestli je tajný kód nastavený pomocí příkazu [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody Client. getsecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Váš tajný kód se teď uloží jako `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec smažte tajný klíč z vašeho trezoru klíčů pomocí [metody Client. beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
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

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)