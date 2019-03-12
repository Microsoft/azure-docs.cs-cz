---
title: Nasazení do služby Azure Functions pomocí modulu plug-in Jenkins Azure Functions
description: Informace o nasazení do služby Azure Functions pomocí modulu plug-in Jenkins Azure Functions
ms.service: jenkins
keywords: jenkins, azure, devops, java, služba azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: 93504de6384be530ba037f662f7b043729aa3f99
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536909"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>Nasazení do služby Azure Functions pomocí modulu plug-in Jenkins Azure Functions

[Služba Azure Functions](/azure/azure-functions/) je výpočetní služba bez serveru. Pomocí služby Azure Functions, můžete spustit kód na vyžádání bez zřizování nebo správy infrastruktury. Tento kurz ukazuje, jak nasadit funkci Java do služby Azure Functions pomocí modulu plug-in Azure Functions.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- **Jenkins server**: Pokud nemáte server Jenkins nainstalovali, najdete v článku [vytvoření serveru Jenkins v Azure](./install-jenkins-solution-template.md).

 > [!TIP]
 > Zdrojový kód používá pro účely tohoto kurzu je umístěn v [úložiště Visual Studio China GitHub](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Vytvoření funkce jazyka Java

Pokud chcete vytvořit funkci jazyka Java pomocí zásobník modulu runtime Java, použijte buď [webu Azure portal](https://portal.azure.com) nebo [rozhraní příkazového řádku Azure](/cli/azure/?view=azure-cli-latest).

Následující kroky ukazují, jak vytvořit funkci jazyka Java pomocí Azure CLI:

1. Vytvořte skupinu prostředků, nahraďte  **&lt;resource_group >** zástupný symbol název skupiny prostředků.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Vytvoření účtu služby Azure storage, nahraďte zástupné symboly příslušnými hodnotami.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Vytvoření aplikace funkcí test, nahraďte zástupné symboly příslušnými hodnotami.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. Aktualizujte modul runtime verze 2.x, nahraďte zástupné symboly příslušnými hodnotami.

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkins

Následující postup vysvětluje, jak připravit Jenkins server:

1. Nasazení [serveru Jenkins](https://aka.ms/jenkins-on-azure) v Azure. Pokud ještě nemáte instance serveru Jenkins nainstalovali, článku, [vytvoření serveru Jenkins v Azure](./install-jenkins-solution-template.md) vás provede procesem.

1. Přihlaste se do instance Jenkinse pomocí protokolu SSH.

1. Instance Jenkinse instalace nástroje maven pomocí následujícího příkazu:

    ```terminal
    sudo apt install -y maven
    ```

1. Instance Jenkinse, nainstalujte [nástrojů Azure Functions Core](/azure/azure-functions/functions-run-local) vydáním následujících příkazů v terminálu řádku:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Na řídicím panelu Jenkinse nainstalujte následující moduly plug-in:

    - Modul plug-in Azure Functions
    - Modul plug-in EnvInject

1. Jenkins musí instanční objekt Azure k ověřování a přístup k prostředkům Azure. Odkazovat [nasazení do služby Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) podrobné pokyny.

1. "Microsoft Azure Service instančního objektu" typ přihlašovacích údajů pomocí instančního objektu Azure, přidejte v Jenkinsu. Odkazovat [nasazení do služby Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) kurzu.

## <a name="fork-the-sample-github-repo"></a>Rozvětvení ukázkového úložiště GitHub

1. [Přihlaste se k úložišti Githubu pro aplikaci liché nebo dokonce ukázka](https://github.com/VSChina/odd-or-even-function.git).

1. V pravém horním rohu v Githubu, zvolte **Forku**.

1. Postupujte podle pokynů dokončit větvení a vyberte účet GitHub.

## <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu Jenkins

V této části vytvoříte [kanálu Jenkins](https://jenkins.io/doc/book/pipeline/).

1. V řídicím panelu Jenkinse vytvořte kanál.

1. Povolit **Příprava prostředí pro běh**.

1. Přidejte následující proměnné prostředí v **vlastnosti obsahu**, nahraďte zástupné symboly příslušnými hodnotami pro vaše prostředí:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. V **kanálu -> definice** vyberte **kanálu skriptu ze Správce řízení služeb**.

1. Zadejte svého forku Githubu adresy URL a skript cestu ("doc/zdroje/jenkins/souboru JenkinsFile") pro použití v [příklad souboru JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Vytvoření a nasazení

Nyní je čas ke spuštění úlohy Jenkins.

1. Nejprve získat autorizační klíč pomocí pokynů [aktivace protokolu HTTP služby Azure Functions a vazby](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) článku.

1. V prohlížeči zadejte adresu URL aplikace. Nahraďte zástupné symboly příslušnými hodnotami a zadat číselnou hodnotu pro  **&lt;input_number >** jako vstup pro funkci jazyka Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Zobrazí se vám výsledky podobně jako následující příklad výstupu (kde liché číslo - 365 - byl použit jako test):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci používat, odstraňte prostředky, které jste vytvořili s následujícím způsobem:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Další postup

Další informace o službě Azure Functions, najdete v následujících prostředků:
> [!div class="nextstepaction"]
> [Dokumentace ke službě Azure Functions](/azure/azure-functions/)
