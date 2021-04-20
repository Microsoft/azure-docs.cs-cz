---
title: Rychlý Start pro Azure Key Vault Klientská knihovna certifikátů – Java
description: Přečtěte si informace o klientské knihovně Azure Key Vault certifikátů pro Java pomocí kroků v tomto rychlém startu.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: c064a35ac6136d728f75b65379c543149cc8189d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750027"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>Rychlý Start: Azure Key Vault klientské knihovny certifikátů pro Java (certifikáty)
Začínáme s klientskou knihovnou certifikátů Azure Key Vault pro Java Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Další prostředky:

* [Zdrojový kód](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [Referenční dokumentace k rozhraní API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Produktová dokumentace](index.yml)
* [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

## <a name="prerequisites"></a>Požadavky
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
V okně konzoly pomocí `mvn` příkazu vytvořte novou konzolovou aplikaci v jazyce Java s názvem `akv-certificates-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Výstup z generování projektu bude vypadat přibližně takto:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Změňte adresář na nově vytvořenou `akv-certificates-java/` složku.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>Instalace balíčku
Otevřete *pom.xml* soubor v textovém editoru. Přidejte následující prvky závislosti do skupiny závislostí.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
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
Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění certifikátu vašemu uživatelskému účtu.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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
Klientská knihovna certifikátů Azure Key Vault pro Java umožňuje správu certifikátů. V části [Příklady kódu](#code-examples) se dozvíte, jak vytvořit klienta, vytvořit certifikát, načíst certifikát a odstranit certifikát.

Celá Konzolová aplikace je [níže](#sample-code).

## <a name="code-examples"></a>Příklady kódu
### <a name="add-directives"></a>Přidat direktivy
Do horní části kódu přidejte následující direktivy:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>Ověření a vytvoření klienta
V tomto rychlém startu se přihlášený uživatel používá k ověření Key Vault, což je upřednostňovaná metoda pro místní vývoj. Pro aplikace nasazené do Azure by se měla App Service nebo virtuální počítač přiřadit spravovaná identita. Další informace najdete v tématu [Přehled spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

V následujícím příkladu je název trezoru klíčů rozbalený do identifikátoru URI trezoru klíčů ve formátu "https:// \<your-key-vault-name\> . Vault.Azure.NET". Tento příklad používá třídu [' DefaultAzureCredential () '](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) , která umožňuje použít stejný kód v různých prostředích s různými možnostmi k poskytnutí identity. Další informace najdete v tématu [výchozí ověřování pověření Azure](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Uložení tajného klíče
Teď, když je vaše aplikace ověřená, můžete v trezoru klíčů vytvořit certifikát pomocí `certificateClient.beginCreateCertificate` metody. To vyžaduje název certifikátu a Zásady certifikátů – k proměnné v této ukázce přiřadíme hodnotu myCertificate `certificateName` a použijeme výchozí zásady.

Vytvoření certifikátu je dlouhodobá operace, pro kterou můžete dotazovat svůj průběh nebo počkat na jeho dokončení.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

Certifikát můžete získat poté, co jste dokončili vytváření prostřednictvím tohoto volání:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Načtení certifikátu
Nyní můžete načíst dříve vytvořený certifikát pomocí `certificateClient.getCertificate` metody.

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

Nyní můžete získat přístup k podrobnostem získaného certifikátu s operacemi `retrievedCertificate.getName` , jako je, `retrievedCertificate.getProperties` atd. I jeho obsah `retrievedCertificate.getCer` .

### <a name="delete-a-certificate"></a>Odstranit certifikát
Nakonec odstraňte certifikát z vašeho trezoru klíčů s `certificateClient.beginDeleteCertificate` metodou, která je také dlouhodobou operací.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
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
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili Trezor klíčů, vytvořili certifikát, načetli ho a pak ho odstranili. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Jak [zabezpečit přístup k trezoru klíčů](../general/security-overview.md)