---
title: 'Kurz: spravovaná identita pro připojení Key Vault'
description: Nastavení spravované identity pro připojení Key Vault k aplikaci pro jarní cloudovou službu Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: bbfafeaf9ce24911bb4893748d456d22e02fc411
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879155"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Kurz: použití spravované identity pro připojení Key Vault k aplikaci pro jarní cloudovou službu Azure

**Tento článek se týká:** ✔️ Java

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro cloudovou aplikaci Azure pro jaře a použít ji pro přístup k Azure Key Vault.

Azure Key Vault můžete použít k bezpečnému ukládání a pečlivému řízení přístupu k tokenům, heslům, certifikátům, klíčům rozhraní API a dalším tajným klíčům vaší aplikace. Spravovanou identitu můžete vytvořit v Azure Active Directory (AAD) a ověřit u jakékoli služby, která podporuje ověřování AAD, včetně Key Vault, aniž byste museli zobrazovat přihlašovací údaje v kódu.

## <a name="prerequisites"></a>Předpoklady

* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* [Instalace rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](/cli/azure/install-azure-cli)
* [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků, která bude obsahovat Key Vault i jarní Cloud, pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Nastavení Key Vault
Pokud chcete vytvořit Key Vault, použijte příkaz [AZ klíčů Create](/cli/azure/keyvault#az-keyvault-create):

> [!Important]
> Každý Key Vault musí mít jedinečný název. V následujících příkladech nahraďte <název vašeho trezoru klíčů> názvem vaší Key Vault.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Poznamenejte si vrácenou `vaultUri` hodnotu, která bude ve formátu "https://<vašeho trezoru klíčů – název>. Vault.Azure.NET". Bude použit v následujícím kroku.

Tajný kód teď můžete do svého Key Vault umístit pomocí příkazu [AZ klíčů trezor tajná sada](/cli/azure/keyvault/secret#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Vytvoření cloudové služby a aplikace Azure na jaře
Po instalaci odpovídajícího rozšíření vytvořte pomocí příkazu Azure CLI instanci cloudu Azure jaře `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
Následující příklad vytvoří aplikaci s názvem `springapp` se spravovanou identitou přiřazenou systémem, jak je požadováno `--assign-identity` parametrem.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Poznamenejte si vrácenou hodnotu `url` , která bude ve formátu `https://<your-app-name>.azuremicroservices.io` . Bude použit v následujícím kroku.


## <a name="grant-your-app-access-to-key-vault"></a>Udělit aplikaci přístup k Key Vault
Slouží `az keyvault set-policy` k udělení správného přístupu v Key Vault vaší aplikaci.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Použijte `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` k odebrání přístupu pro aplikaci po zakázání spravované identity přiřazené systémem.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Sestavení ukázkové aplikace pro spouštění pomocí jarního startu Starter
Tato aplikace bude mít přístup k získání tajných kódů z Azure Key Vault. Použijte úvodní aplikaci: [Azure Key Vault tajných klíčů po spuštění](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault se přidá jako instance jarního **objekt propertysourceu**.  Tajné kódy uložené v Azure Key Vault lze pohodlně přizpůsobovat a používat jako jakékoli externě nastavené konfigurační vlastnosti, jako jsou například vlastnosti v souborech. 

1. Vygenerujte vzorový projekt z start.spring.io s Azure Key Vault jarní Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Zadejte své Key Vault do aplikace. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Pokud chcete používat spravovanou identitu pro aplikace Azure pro jarní Cloud, přidejte vlastnosti s níže uvedeným obsahem do pole src/Main/Resources/Application. Properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Je nutné přidat adresu URL trezoru klíčů do `application.properties` výše uvedeného seznamu. Jinak nesmí být adresa URL trezoru klíčů zachycena během běhu.

3. Přidejte příklad kódu do src/Main/Java/com/example/demo/DemoApplication. Java. Načte připojovací řetězec z Key Vault. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Pokud otevřete pom.xml, zobrazí se závislost `azure-keyvault-secrets-spring-boot-starter` . Přidejte tuto závislost do projektu v pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Zabalit ukázkovou aplikaci. 

    ```azurecli
    mvn clean package
    ```

5. Teď můžete aplikaci nasadit do Azure pomocí příkazu Azure CLI  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Chcete-li otestovat aplikaci, přejděte k veřejnému koncovému bodu nebo ke koncovému bodu testu.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Zobrazí se zpráva "úspěšně obdržena hodnota tajného řetězce connectionString z Key Vault https://<název trezoru klíčů>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; Database = DATABASE;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Sestavit ukázkovou aplikaci pro spouštění pružin pomocí sady Java SDK

Tato ukázka může nastavit a získat tajné kódy z Azure Key Vault. [Klientská knihovna Azure Key Vault tajných klíčů pro jazyk Java](/java/api/overview/azure/security-keyvault-secrets-readme) poskytuje podporu ověřování tokenů Azure Active Directory napříč sadou Azure SDK. Poskytuje sadu **TokenCredential** implementace, které se dají použít k sestavování klientů Azure SDK pro podporu ověřování pomocí tokenu AAD.

Klientská knihovna Azure Key Vault tajných klíčů vám umožní bezpečně ukládat a řídit přístup k tokenům, heslům, klíčům rozhraní API a dalším tajným klíčům. Knihovna nabízí operace vytvoření, načtení, aktualizace, odstranění, vyprázdnění, zálohování, obnovení a výpisu tajných kódů a jejich verzí.

1. Naklonujte ukázkový projekt. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Zadejte své Key Vault do aplikace. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Pokud chcete používat spravovanou identitu pro aplikace Azure pro jarní Cloud, přidejte vlastnosti s následujícím obsahem do *Src/Main/Resources/Application. Properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Zahrňte [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder) k získání tokenu z Azure Active Directory a [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) pro nastavení nebo získání tajných kódů z Key Vault ve vašem kódu.

    Získejte příklad z [MainController. Java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) klonovaného ukázkového projektu.

    `azure-identity`Do pom.xml také zahrnout a `azure-security-keyvault-secrets` jako závislost. Získejte příklad z [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) naklonovaného ukázkového projektu. 

4. Zabalit ukázkovou aplikaci. 

    ```azurecli
    mvn clean package
    ```

5. Nyní Nasaďte aplikaci do Azure pomocí příkazu Azure CLI  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. K otestování vaší aplikace se dostanete ke veřejnému koncovému bodu nebo testovacímu bodu. 

    Nejprve Získejte hodnotu vašeho tajného kódu, který jste nastavili v Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Zobrazí se zpráva "úspěšně obdržena hodnota tajného řetězce connectionString z Key Vault https://<název trezoru klíčů>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; Database = DATABASE;".

    Nyní vytvořte tajný klíč a pak ho načtěte pomocí sady Java SDK. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Zobrazí se zpráva "úspěšně obdržena hodnota tajného testu z Key Vault https://<název trezoru klíčů>. vault.azure.net: úspěch". 

## <a name="next-steps"></a>Další kroky

* [Přístup k objektu BLOB úložiště se spravovanou identitou v Azure jaře cloudu](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Postup povolení spravované identity přiřazené systémem pro aplikaci Azure jaře Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Další informace o spravovaných identitách pro prostředky Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Ověřování Azure jaře cloudu s Key Vault v akcích GitHubu](./spring-cloud-github-actions-key-vault.md)
