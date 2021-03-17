---
title: Rychlý Start-Azure Key Vault Klientská knihovna certifikátů pro JavaScript (verze 4)
description: Naučte se vytvářet, načítat a odstraňovat certifikáty z trezoru klíčů Azure pomocí klientské knihovny JavaScript.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1064c6a1e2dddaae98e94ccceca7b1d550897393
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930850"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Rychlý Start: Azure Key Vault klientské knihovny pro certifikáty pro JavaScript (verze 4)

Začínáme s klientskou knihovnou certifikátů Azure Key Vault pro JavaScript [Azure Key Vault](../general/overview.md) je cloudová služba, která poskytuje zabezpečené úložiště pro certifikáty. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Trezory klíčů Azure můžete vytvářet a spravovat přes web Azure Portal. V tomto rychlém startu se naučíte vytvářet, načítat a odstraňovat certifikáty z trezoru klíčů Azure pomocí klientské knihovny JavaScriptu.

Prostředky klientské knihovny Key Vault:

[Referenční dokumentace k](/javascript/api/overview/azure/key-vault-index)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Další informace o Key Vault a certifikátech najdete v tématech:
- [Přehled Key Vault](../general/overview.md)
- [Přehled certifikátů](about-certificates.md).

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

V okně konzoly nainstalujte Azure Key Vault [Knihovna certifikátů](https://www.npmjs.com/package/@azure/keyvault-certificates) pro Node.js.

```azurecli
npm install @azure/keyvault-certificates
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

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění certifikátu vašemu uživatelskému účtu.

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Příklady kódu

Následující ukázky kódu vám ukážou, jak vytvořit klienta, nastavit certifikát, načíst certifikát a odstranit certifikát. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) z [knihovny Azure identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace o ověřování do trezoru klíčů najdete v tématu [Příručka pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Přidejte následující kód do funkce main ().

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Uložení certifikátu

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit certifikát pomocí [metody beginCreateCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) , která vyžaduje název certifikátu a[zásady](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) certifikátů zásad certifikátů s [vlastnostmi zásad certifikátu](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties) .

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Pokud název certifikátu existuje, vytvoří se nad kódem nová verze tohoto certifikátu.
### <a name="retrieve-a-certificate"></a>Načtení certifikátu

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody getcertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Odstranit certifikát

Nakonec smažte a vyprázdnit certifikát z trezoru klíčů pomocí metod [beginDeleteCertificate] https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) a [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) .

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Ukázka kódu

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili certifikát a načetli jste tento certifikát. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si [Přehled certifikátů](about-certificates.md) .
- Přečtěte si [kurz Key Vault přístupu z App Service aplikace](../general/tutorial-net-create-vault-azure-web-app.md) .
- Podívejte se na [kurz Key Vault přístupu z virtuálního počítače](../general/tutorial-net-virtual-machine.md) .
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
