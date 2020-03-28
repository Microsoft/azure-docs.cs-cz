---
title: Kurz pro použití odkazů na konfigurační klíč Azure V aplikaci Java Spring Boot | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak používat odkazy azure app konfigurace trezoru klíčů z aplikace Java Spring Boot
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b6b6d10165eed331c397e17a18e382b095e1f74f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216750"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Kurz: Použití odkazů na trezor klíčů v aplikaci Java Spring

V tomto kurzu se dozvíte, jak používat službu Konfigurace aplikací Azure společně s Azure Key Vault. Konfigurace aplikací a trezor klíčů jsou doplňkové služby používané vedle sebe ve většině nasazení aplikací.

Konfigurace aplikace pomáhá využívat služby společně vytvořením klíčů, které odkazují na hodnoty uložené v trezoru klíčů. Když konfigurace aplikace vytvoří takové klíče, ukládá identifikátory URI hodnoty trezoru klíčů spíše než hodnoty samotné.

Vaše aplikace používá zprostředkovatele klienta Konfigurace aplikace k načtení odkazů trezoru klíčů, stejně jako u všech ostatních klíčů uložených v konfiguraci aplikace. V tomto případě hodnoty uložené v konfiguraci aplikace jsou identifikátory URI, které odkazují na hodnoty v trezoru klíčů. Nejedná se o hodnoty nebo pověření trezoru klíčů. Vzhledem k tomu, že zprostředkovatel klienta rozpozná klíče jako odkazy trezoru klíčů, používá trezor klíčů k načtení jejich hodnot.

Vaše aplikace je zodpovědná za správné ověření konfigurace aplikace a trezoru klíčů. Tyto dvě služby nekomunikují přímo.

Tento kurz ukazuje, jak implementovat odkazy trezoru klíčů ve vašem kódu. Staví na webové aplikaci zavedené v rychlých startech. Než budete pokračovat, nejprve [dokončete vytvoření aplikace Java Spring s konfigurací aplikací.](./quickstart-java-spring-app.md)

Můžete použít libovolný editor kódu k tomu kroky v tomto kurzu. [Visual Studio Code](https://code.visualstudio.com/) je například editor kódu pro různé platformy, který je k dispozici pro operační systémy Windows, macOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte konfigurační klíč aplikace, který odkazuje na hodnotu uloženou v trezoru klíčů.
> * Přístup k hodnotě tohoto klíče z aplikace Java Spring.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Podporovaná [java development kit (JDK)](https://docs.microsoft.com/java/azure/jdk) s verzí 8.
* [Apache Maven](https://maven.apache.org/download.cgi) verze 3.0 nebo vyšší.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V levém horním rohu portálu Azure vyberte možnost **Vytvořit prostředek:**

    ![Výstup po dokončení vytvoření trezoru klíčů](./media/quickstarts/search-services.png)
1. Do vyhledávacího pole zadejte **Trezor klíčů**.
1. V seznamu výsledků vyberte **trezory kláves** vlevo.
1. V **trezorech klíčů**vyberte **Přidat**.
1. Vpravo v **tématu Vytvořit trezor klíčů**zadejte následující informace:
    * Výběrem **možnosti Předplatné** vyberte předplatné.
    * Ve **skupině prostředků**vyberte Vytvořit **nový** a zadejte název skupiny prostředků.
    * V **názvu trezoru klíčů**je vyžadován jedinečný název. V tomto kurzu zadejte **Contoso-vault2**.
    * V rozevíracím seznamu **Oblast** zvolte umístění.
1. Ostatní možnosti **úložiště klíčů** ponechte s jejich výchozími hodnotami.
1. Vyberte **Vytvořit**.

V tomto okamžiku je váš účet Azure jediný oprávněný pro přístup k tomuto novému trezoru.

![Výstup po dokončení vytvoření trezoru klíčů](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Chcete-li do trezoru přidat tajný klíč, musíte provést pouze několik dalších kroků. V takovém případě přidejte zprávu, kterou můžete použít k testování načítání trezoru klíčů. Zpráva se nazývá **Zpráva**a uložíte do ní hodnotu "Hello from Key Vault".

1. Na stránkách vlastností trezoru klíčů vyberte **položku Tajné položky**.
1. Vyberte **Generovat/importovat**.
1. V **podokně Vytvořit tajný klíč** zadejte následující hodnoty:
    * **Možnosti nahrávání**: Zadejte **ručně**.
    * **Název**: Zadejte **zprávu**.
    * **Hodnota**: Zadejte **Hello z trezoru klíčů**.
1. Ponechte ostatní **Vytvořte tajné** vlastnosti s jejich výchozí hodnoty.
1. Vyberte **Vytvořit**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Přidání odkazu trezoru klíčů do konfigurace aplikace

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **Všechny prostředky**a pak vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**.

1. Vyberte **+ Vytvořit** > **odkaz na trezor klíčů**a zadejte následující hodnoty:
    * **Klíč**: Select **/application/config.keyvaultmessage**
    * **Popisek**: Ponechejte tuto hodnotu prázdnou.
    * **Odběr**, **Skupina prostředků**a **Trezor klíčů**: Zadejte hodnoty odpovídající hodnotám v trezoru klíčů, který jste vytvořili v předchozí části.
    * **Tajný klíč**: Vyberte tajný klíč s názvem **Zpráva,** který jste vytvořili v předchozí části.

## <a name="connect-to-key-vault"></a>Připojení k trezoru klíčů

1. V tomto kurzu použijete instanční objekt pro ověřování trezoru klíčů. Chcete-li vytvořit tento instanční objekt, použijte příkaz [AZ az sp az azure Azure cli:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Tato operace vrátí řadu párů klíč/hodnota:

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

1. Spuštěním následujícího příkazu umožníte instančnímu objektu přístup k trezoru klíčů:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Spusťte následující příkaz, abyste získali id objektu, a přidejte ho do konfigurace aplikace.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Vytvořte následující proměnné prostředí pomocí hodnot pro instanční objekt, které byly zobrazeny v předchozím kroku:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Tato pověření trezoru klíčů se používají pouze v rámci vaší aplikace.  Aplikace se ověřuje přímo pomocí trezoru klíčů pomocí těchto přihlašovacích údajů bez zapojení služby Konfigurace aplikace.  Trezor klíčů poskytuje ověřování pro vaši aplikaci i službu Konfigurace aplikace bez sdílení nebo vystavení klíčů.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizace kódu pro použití odkazu trezoru klíčů

1. Otevřete *soubor MessageProperties.java*. Přidejte novou proměnnou s názvem *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Otevřete *soubor HelloController.java*. Aktualizujte metodu *getMessage* tak, aby zahrnovala zprávu načtenou z trezoru klíčů.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Vytvořte nový soubor s názvem *AzureCredentials.java* a přidejte níže uvedený kód.

    ```java
    package com.example;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Vytvořte nový soubor s názvem *AppConfiguration.java*. A přidejte kód níže.

    ```java
    package com.example;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Vytvořte nový soubor ve vašem adresáři META-INF s názvem *spring.factories* a přidejte.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.AppConfiguration
    ```

1. Sestavte si aplikaci Spring Boot s Maven a spusťte ji, například:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Po spuštění aplikace použijte *curl* k testování aplikace, například:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Zobrazí se zpráva, kterou jste zadali v úložišti konfigurace aplikací. Zobrazí se také zpráva, kterou jste zadali do trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili klíč konfigurace aplikace, který odkazuje na hodnotu uloženou v trezoru klíčů. Chcete-li se dozvědět, jak používat příznaky funkcí v aplikaci Java Spring, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./quickstart-feature-flag-spring-boot.md)
