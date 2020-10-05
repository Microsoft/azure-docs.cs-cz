---
title: Rychlý Start – Azure Key Vault klientskou knihovnu pro Java
description: Poskytuje kritéria formátování a obsahu pro psaní rychlých startů pro klientské knihovny Azure SDK.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 6c29141a2e255588ffa581b84ffeb4ddd7fdb703
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87324705"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Rychlý Start: Azure Key Vault Klientská knihovna pro Java

Začněte s knihovnou klienta Azure Key Vault pro Java. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Key Vault pro jazyk Java:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvářejte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky škálování cloudu a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte ověřený HSM úrovně 2 FIPS 140-2.

Další prostředky:

* [Zdrojový kód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault)
* [Referenční dokumentace k rozhraní API](https://azure.github.io/azure-sdk-for-java)
* [Produktová dokumentace](index.yml)
* [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) verze 8 nebo vyšší
- [Apache Maven](https://maven.apache.org)
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/)

V tomto rychlém startu se předpokládá, že používáte [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) a [Apache Maven](https://maven.apache.org) v okně terminálu pro Linux.

## <a name="setting-up"></a>Nastavení

### <a name="create-new-java-console-app"></a>Vytvořit novou konzolovou aplikaci v jazyce Java

V okně konzoly pomocí `mvn` příkazu vytvořte novou konzolovou aplikaci v jazyce Java s názvem `akv-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Výstup z generování projektu bude vypadat přibližně takto:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Změňte adresář na nově vytvořenou integrace-Java/Folder.

```console
cd akv-java
```

### <a name="install-the-package"></a>Instalace balíčku

Otevřete *pom.xml* soubor v textovém editoru. Přidejte následující prvky závislosti do skupiny závislostí.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
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

Klientská knihovna Azure Key Vault pro jazyk Java umožňuje správu klíčů a souvisejících prostředků, jako jsou certifikáty a tajné klíče. Následující ukázka kódu vám ukáže, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Celá Konzolová aplikace je [níže](#sample-code).

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověřování pro váš Trezor klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí v kroku [nastavit proměnné prostředí](#set-environmental-variables) výše. Název trezoru klíčů se rozšíří na identifikátor URI trezoru klíčů ve formátu `https://<your-key-vault-name>.vault.azure.net` .

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí `secretClient.setSecret` metody. To vyžaduje název tajného kódu – k `secretName` proměnné v této ukázce přiřadíme hodnotu "mySecret".  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Můžete ověřit, jestli je tajný kód nastavený pomocí příkazu [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí `secretClient.getSecret` metody.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Přístup k hodnotě načteného tajného kódu teď můžete získat pomocí `retrievedSecret.getValue()` .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec klíč odstraníme z vašeho trezoru klíčů pomocí `secretClient.beginDeleteSecret` metody.

```java
secretClient.beginDeleteSecret(secretName);
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

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
