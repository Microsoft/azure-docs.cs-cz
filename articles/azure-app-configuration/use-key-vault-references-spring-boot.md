---
title: Kurz použití konfigurace aplikace Azure Key Vault odkazy v aplikaci Java pružiny pro spouštění | Microsoft Docs
description: V tomto kurzu se naučíte používat odkazy na Key Vault Azure App Configuration z aplikace Java na jaře.
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
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 849f25f6fdd3fef2e1ebca7dae397d96e6849f10
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748852"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Kurz: použití odkazů Key Vault v aplikaci Java pružiny

V tomto kurzu se naučíte používat službu Azure App Configuration Service společně s Azure Key Vault. Konfigurace aplikace a Key Vault jsou doplňkové služby, které jsou používány souběžně ve většině nasazení aplikace.

Konfigurace aplikací pomáhá používat služby dohromady vytvořením klíčů, které odkazují na hodnoty uložené v Key Vault. Když konfigurace aplikace tyto klíče vytvoří, uloží identifikátory URI Key Vault hodnoty místo samotných hodnot.

Vaše aplikace používá poskytovatele klienta konfigurace aplikace k načtení Key Vault odkazů, stejně jako u všech ostatních klíčů uložených v konfiguraci aplikace. V tomto případě hodnoty uložené v konfiguraci aplikace jsou identifikátory URI, které odkazují na hodnoty v Key Vault. Nejsou Key Vault hodnoty ani přihlašovací údaje. Vzhledem k tomu, že poskytovatel klienta rozpozná klíče jako odkazy Key Vault, používá Key Vault k načtení jejich hodnot.

Vaše aplikace zodpovídá za to, že se správně ověřuje jak konfigurace aplikace, tak i Key Vault. Tyto dvě služby nekomunikují přímo.

V tomto kurzu se dozvíte, jak implementovat Key Vault odkazy v kódu. Sestavuje se ve webové aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace s pružinou v jazyce Java pomocí konfigurace aplikace](./quickstart-java-spring-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. Například [Visual Studio Code](https://code.visualstudio.com/) je editor kódu pro různé platformy, který je k dispozici pro operační systémy Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte konfigurační klíč aplikace, který odkazuje na hodnotu uloženou v Key Vault.
> * Přístup k hodnotě tohoto klíče z jarní aplikace Java.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Podporovaná [sada Java Development Kit (JDK)](/java/azure/jdk) s verzí 8.
* [Apache Maven](https://maven.apache.org/download.cgi) verze 3,0 nebo vyšší.

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V levém horním rohu Azure Portal vyberte možnost **vytvořit prostředek** :

    ![Výstup po dokončení vytvoření trezoru klíčů](./media/quickstarts/search-services.png)
1. Do vyhledávacího pole zadejte **Key Vault** .
1. V seznamu výsledků vyberte na levé straně **trezory klíčů** .
1. V **trezorech klíčů** vyberte **Přidat** .
1. Na pravé straně v části **Vytvoření trezoru klíčů** zadejte následující informace:
    * Vyberte **předplatné** a zvolte předplatné.
    * V případě **skupiny prostředků** vyberte **vytvořit novou** a zadejte název skupiny prostředků.
    * V **názvu trezoru klíčů** je vyžadován jedinečný název. Pro tento kurz zadejte **Contoso-vault2** .
    * V rozevíracím seznamu **oblast** vyberte umístění.
1. Ostatní možnosti **Vytvoření trezoru klíčů** ponechte výchozí hodnoty.
1. Vyberte **Vytvořit** .

V tomto okamžiku je váš účet Azure jediným autorizovaným oprávněním pro přístup k tomuto novému trezoru.

![Výstup po dokončení vytvoření trezoru klíčů](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, musíte provést několik dalších kroků. V takovém případě přidejte zprávu, kterou můžete použít k otestování Key Vault načítání. Zpráva se nazývá **zpráva** a v ní uložíte hodnotu Hello z Key Vault.

1. Na stránkách vlastností Key Vault vyberte **tajné klíče** .
1. Vyberte **Generovat/importovat** .
1. V podokně **vytvořit tajný klíč** zadejte následující hodnoty:
    * **Možnosti nahrání** : zadejte **Ruční** .
    * **Název** : zadejte **zprávu** .
    * **Hodnota** : zadejte **hello z Key Vault** .
1. Ponechte druhé **vytvořit vlastnosti tajného klíče** s výchozími hodnotami.
1. Vyberte **Vytvořit** .

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Přidat odkaz Key Vault do konfigurace aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky** a pak vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace** .

1. Vyberte **+ vytvořit**  >  **odkaz na Trezor klíčů** a pak zadejte následující hodnoty:
    * **Klíč** : vyberte **/Application/config.keyvaultmessage**
    * **Popisek** : Nechte tuto hodnotu prázdnou.
    * **Předplatné** , **Skupina prostředků** a **Trezor klíčů** : zadejte hodnoty odpovídající hodnotám v trezoru klíčů, který jste vytvořili v předchozí části.
    * **Tajný kód** : vyberte tajný kód s názvem **zpráva** , kterou jste vytvořili v předchozí části.

## <a name="connect-to-key-vault"></a>Připojení k Key Vault

1. V tomto kurzu použijete k ověřování Key Vault instanční objekt. Tento instanční objekt vytvoříte pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Tato operace vrátí řadu párů klíč/hodnota:

    ```json
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

1. Spusťte následující příkaz, který instančnímu objektu umožní přístup k trezoru klíčů:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Spuštěním následujícího příkazu Získejte ID objektu a pak ho přidejte do konfigurace aplikace.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Vytvořte proměnné prostředí **AZURE_CLIENT_ID** , **AZURE_CLIENT_SECRET** a **AZURE_TENANT_ID** . Použijte hodnoty pro instanční objekt, který byl zobrazen v předchozích krocích. Na příkazovém řádku spusťte následující příkazy a restartujte příkazový řádek, aby se změna projevila:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Tyto Key Vault přihlašovací údaje se používají jenom v rámci vaší aplikace.  Vaše aplikace se ověřuje přímo pomocí Key Vault pomocí těchto přihlašovacích údajů bez použití služby konfigurace aplikace.  Key Vault poskytuje ověřování pro vaši aplikaci i službu konfigurace vaší aplikace bez sdílení nebo vystavování klíčů.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizace kódu pro použití odkazu na Key Vault

1. Vytvořte proměnnou prostředí s názvem **APP_CONFIGURATION_ENDPOINT** . Nastavte jeho hodnotu na koncový bod úložiště konfigurace aplikace. Koncový bod najdete v okně **přístupové klíče** v Azure Portal. Restartováním příkazového řádku umožníte, aby se změna projevila. 


1. Otevřete *rutinu Bootstrap. Properties* ve složce *Resources* . Aktualizujte tento soubor, aby se použila hodnota **APP_CONFIGURATION_ENDPOINT** . Odebere všechny odkazy na připojovací řetězec v tomto souboru. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Otevřete *MessageProperties. Java* . Přidejte novou proměnnou s názvem *keyVaultMessage* :

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Otevřete *HelloController. Java* . Aktualizujte metodu *GetMessage* tak, aby zahrnovala zprávu načtenou z Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Vytvořte nový soubor s názvem *AzureCredentials. Java* a přidejte následující kód.

    ```java
    package com.example.demo;

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

1. Vytvořte nový soubor s názvem *AppConfiguration. Java* . A níže přidejte kód.

    ```java
    package com.example.demo;

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

1. Vytvořte nový soubor v adresáři Resources adresáře META-INF s názvem *pružiny. továrens* a přidejte následující kód.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Sestavte aplikaci pro jarní spouštění pomocí Maven a spusťte ji například takto:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Po spuštění aplikace použijte k otestování aplikace *kudrlinkou* . Příklad:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Zobrazí se zpráva, kterou jste zadali v úložišti konfigurace aplikace. Zobrazí se také zpráva, kterou jste zadali v Key Vault.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili konfigurační klíč aplikace, který odkazuje na hodnotu uloženou v Key Vault. Pokud se chcete dozvědět, jak používat příznaky funkcí v aplikaci Java pružiny, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./quickstart-feature-flag-spring-boot.md)
