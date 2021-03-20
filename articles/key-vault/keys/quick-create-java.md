---
title: Rychlý Start – Azure Key Vault klíčovou knihovnou klienta pro Java
description: Poskytuje rychlý Start pro klientskou knihovnu Azure Key Vault Keys pro jazyk Java.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: cb5abf59c446ef0835375bac45d1e852144a6f28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935270"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Rychlý Start: Azure Key Vault klíč klientské knihovny pro Java
Začněte s klíčovou knihovnou klienta Azure Key Vault pro Java. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Další prostředky:

* [Zdrojový kód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Referenční dokumentace k rozhraní API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Produktová dokumentace](index.yml)
* [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Předpoklady
- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) verze 8 nebo vyšší
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

V tomto rychlém startu se předpokládá, že používáte [Azure CLI](/cli/azure/install-azure-cli) a [Apache Maven](https://maven.apache.org) v okně terminálu pro Linux.

## <a name="setting-up"></a>Nastavení
V tomto rychlém startu se k ověření uživatele do služeb Azure používá knihovna Azure identity s rozhraním příkazového řádku Azure. Vývojáři můžou k ověřování hovorů použít taky Visual Studio nebo Visual Studio Code, další informace najdete v tématu [ověření klienta pomocí klientské knihovny Azure identity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Přihlášení k Azure
1. Spusťte příkaz `login`.

    ```azurecli-interactive
    az login
    ```

   Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

   V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu.

2. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

### <a name="create-a-new-java-console-app"></a>Vytvoření nové konzolové aplikace v jazyce Java
V okně konzoly pomocí `mvn` příkazu vytvořte novou konzolovou aplikaci v jazyce Java s názvem `akv-keys-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Výstup z generování projektu bude vypadat přibližně takto:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Změňte adresář na nově vytvořenou `akv-keys-java/` složku.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Instalace balíčku
Otevřete *pom.xml* soubor v textovém editoru. Přidejte následující prvky závislosti do skupiny závislostí.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů
Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje klíčovým oprávněním k vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí
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

## <a name="object-model"></a>Objektový model
Klíčová knihovna klienta Azure Key Vault pro Java umožňuje správu klíčů. V části [Příklady kódu](#code-examples) se dozvíte, jak vytvořit klienta, vytvořit klíč, načíst klíč a odstranit klíč.

Celá Konzolová aplikace je [níže](#sample-code).

## <a name="code-examples"></a>Příklady kódu
### <a name="add-directives"></a>Přidat direktivy
Do horní části kódu přidejte následující direktivy:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta
V tomto rychlém startu se přihlášený uživatel používá k ověření Key Vault, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V následujícím příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) , která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace najdete v tématu [výchozí ověřování pověření Azure](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Vytvoření klíče
Teď, když je vaše aplikace ověřená, můžete pomocí metody vytvořit klíč v trezoru klíčů `keyClient.createKey` . To vyžaduje název pro klíč a typ klíče – přiřadíme proměnné hodnotu myKey `keyName` a `KeyType` v této ukázce použijeme RSA.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Můžete ověřit, že se klíč nastavil pomocí příkazu [AZ klíčů trezor Key show](/cli/azure/keyvault/key?#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Načtení klíče
Nyní můžete načíst dříve vytvořený klíč pomocí `keyClient.getKey` metody.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Nyní můžete získat přístup k podrobnostem získaného klíče s operacemi `retrievedKey.getProperties` , jako je, `retrievedKey.getKeyOperations` atd.

### <a name="delete-a-key"></a>Odstranění klíče
Nakonec klíč odstraníme z vašeho trezoru klíčů pomocí `keyClient.beginDeleteKey` metody.

Odstranění klíče je dlouhodobá operace, pro kterou můžete dotazovat svůj průběh nebo počkat na jeho dokončení.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Odstranění klíče můžete ověřit pomocí příkazu [AZ klíčů trezor Key show](/cli/azure/keyvault/key?#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
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
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili Trezor klíčů, vytvořili klíč, načetli ho a pak ho odstranili. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Jak [zabezpečit přístup k trezoru klíčů](../general/secure-your-key-vault.md)