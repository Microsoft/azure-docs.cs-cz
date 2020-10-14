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
ms.openlocfilehash: 87d7bbaa40226e02726b92cf7f7705c8028149f7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019626"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Rychlý Start: Azure Key Vaulta tajná Klientská knihovna pro Java

Začínáme s klientskou knihovnou Azure Key Vault tajných klíčů pro Java Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Další prostředky:

* [Zdrojový kód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault)
* [Referenční dokumentace k rozhraní API](https://azure.github.io/azure-sdk-for-java)
* [Produktová dokumentace](index.yml)
* [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) verze 8 nebo vyšší
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

V tomto rychlém startu se předpokládá, že používáte [Azure CLI](/cli/azure/install-azure-cli) a [Apache Maven](https://maven.apache.org) v okně terminálu pro Linux.

## <a name="setting-up"></a>Nastavení

Tento rychlý Start používá Azure identity Library s Azure CLI k ověřování uživatele ve službách Azure. Vývojáři můžou k ověřování hovorů použít taky Visual Studio nebo Visual Studio Code, další informace najdete v tématu [ověření klienta pomocí klientské knihovny Azure identity](https://docs.microsoft.com/java/api/overview/azure/identity-readme) .

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Spusťte příkaz `login`.

    ```azurecli-interactive
    az login
    ```

    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

    V opačném případě otevřete stránku prohlížeče na adrese [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu.

2. Přihlaste se pomocí přihlašovacích údajů vašeho účtu v prohlížeči.

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

#### <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění tajného kódu vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Tato aplikace používá název trezoru klíčů jako proměnnou prostředí s názvem `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS nebo Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault tajných klíčů pro Java umožňuje správu tajných kódů. V části [Příklady kódu](#code-examples) se dozvíte, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

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

V tomto rychlém startu se přihlášený uživatel používá k ověření v trezoru klíčů, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V níže uvedeném příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu  [' DefaultAzureCredential () '](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) , která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace najdete v tématu [výchozí ověřování pověření Azure](https://docs.microsoft.com/java/api/overview/azure/identity-readme). 

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

Můžete ověřit, jestli je tajný kód nastavený pomocí příkazu [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?#az-keyvault-secret-show) :

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

V případě, že se tajný klíč nachází, můžete ověřit pomocí příkazu [AZ klíčů trezor tajné zobrazení](/cli/azure/keyvault/secret?#az-keyvault-secret-show) :

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
- Jak [zabezpečit přístup k trezoru klíčů](../general/secure-your-key-vault.md)
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
