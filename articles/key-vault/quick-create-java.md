---
title: Úvodní příručka – klientská knihovna Azure Key Vault pro Javu
description: Poskytuje kritéria formátu a obsahu pro psaní rychlých startů pro klientské knihovny Sady Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: fd2373eb1c5cd5b48651c9215b5d6ad85c7acd68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79457266"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Úvodní příručka: Klientská knihovna Azure Key Vault pro Javu

Začínáme s klientskou knihovnou Azure Key Vault pro Jazyk Java. Podle následujících kroků nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Key Vault pro Jazyk Java:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvořte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky cloudovému škálování a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte fips 140-2 úroveň 2 ověřené hmeony.

[Referenční](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | [dokumentace](https://azure.github.io/azure-sdk-for-java) | rozhraní API zdrojového kódu[Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets) [dokumentace k](index.yml) | produktu

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) verze 8 nebo vyšší
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview)

Tento rychlý start předpokládá, že používáte [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) a [Apache Maven](https://maven.apache.org) v okně terminálu Linuxu.

## <a name="setting-up"></a>Nastavení

### <a name="create-new-java-console-app"></a>Vytvoření nové konzolové aplikace Java

V okně konzoly `mvn` vytvořte pomocí příkazu novou konzolovou aplikaci Java s názvem `akv-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Výstup z generování projektu bude vypadat nějak takto:

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

Změňte svůj adresář na nově vytvořenou složku akv-java/.

```console
cd akv-java
```

### <a name="install-the-package"></a>Instalace balíčku

Otevřete soubor *pom.xml* v textovém editoru. Přidejte následující prvky závislostí do skupiny závislostí.

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

Tento rychlý start používá předem vytvořený trezor klíčů Azure. Trezor klíčů můžete vytvořit podle kroků v [rychlém startu Azure CLI](quick-create-cli.md), [rychlém startu Azure PowerShellu](quick-create-powershell.md)nebo [rychlém startu na webu Azure Portal](quick-create-portal.md). Případně můžete spustit příkazy Azure CLI níže.

> [!Important]
> Každý trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <> názvu trezoru s jedinečným klíčem.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nejjednodušší způsob ověření cloudové aplikace je se spravovanou identitou; podrobnosti najdete [v tématu Použití spravované identity služby App Service pro přístup k azure key vault](managed-identity.md) pro podrobnosti. Z důvodu jednoduchosti však tento rychlý start vytvoří desktopovou aplikaci, která vyžaduje použití instančního objektu a zásady řízení přístupu.

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

Poznamenejte si clientId, clientSecret a tenantId, jako budeme používat v dalších dvou krocích.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Poskytnutí přístupu k objektu služeb trezoru klíčů

Vytvořte zásady přístupu pro trezor klíčů, který uděluje oprávnění k instančnímu objektu klienta příkazu [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Udělte instančníobjekt get, list a nastavit oprávnění pro klíče i tajné klíče.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Nastavení proměnných prostředí

Metoda DefaultAzureCredential v naší aplikaci závisí na `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`třech `AZURE_TENANT_ID`proměnných prostředí: , , a . použijte nastavit tyto proměnné clientId, clientSecret a tenantId hodnoty, které jste si poznamenali v [vytvořit instanční objekt](#create-a-service-principal) krok výše. Pomocí `export VARNAME=VALUE` formátu nastavte proměnné prostředí. (Tato metoda pouze nastaví proměnné pro aktuální prostředí a procesy vytvořené z prostředí; trvale `/etc/environment ` přidat tyto proměnné do vašeho prostředí, upravit soubor.) 

Název trezoru klíčů budete muset také uložit `KEY_VAULT_NAME`jako proměnnou prostředí nazvanou .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro Jazyk Java umožňuje spravovat klíče a související prostředky, jako jsou certifikáty a tajné klíče. Ukázky kódu níže vám ukáže, jak vytvořit klienta, nastavit tajný klíč, načíst tajný klíč a odstranit tajný klíč.

Celá konzolová aplikace je [níže](#sample-code).

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidání směrnic

Na začátek kódu přidejte následující direktivy:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta

Ověření do trezoru klíčů a vytvoření klienta trezoru klíčů závisí na proměnných prostředí v kroku [Nastavit proměnné prostředí](#set-environmental-variables) výše. Název trezoru klíčů je rozšířen na identifikátor URI `https://<your-key-vault-name>.vault.azure.net`trezoru klíčů ve formátu .

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Uložení tajného klíče

Nyní, když je aplikace ověřena, můžete vložit tajný `secretClient.setSecret` klíč do trezoru klíčů pomocí metody. To vyžaduje název pro tajný klíč – jsme přiřadili `secretName` hodnotu "mySecret" proměnné v této ukázce.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Pomocí příkazu [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) můžete ověřit, zda byl tajný klíč nastaven:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného klíče

Nyní můžete načíst dříve nastavenou hodnotu `secretClient.getSecret` s metodou.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Nyní můžete získat přístup k hodnotě `retrievedSecret.getValue()`načteného tajného klíče pomocí aplikace .

### <a name="delete-a-secret"></a>Odstranění tajného klíče

Nakonec odstraníme tajný klíč z trezoru `secretClient.beginDeleteSecret` klíčů pomocí metody.

```java
secretClient.beginDeleteSecret(secretName);
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

V tomto rychlém startu jste vytvořili trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](key-vault-overview.md)
- Podívejte se na [průvodce vývojářem azure key vaultu](key-vault-developers-guide.md)
- Informace o [klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [doporučených postupů azure key vaultu](key-vault-best-practices.md)
