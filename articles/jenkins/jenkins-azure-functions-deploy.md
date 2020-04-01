---
title: Nasazení do funkcí Azure pomocí modulu plug-in Jenkins Azure Functions
description: Přečtěte si, jak nasadit do funkcí Azure pomocí modulu plug-in Jenkins Azure Functions
keywords: jenkins, azure, devops, java, azure funkce
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 731bac13a596bbeaf970b3f6ce976a582d1f11ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78250907"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Nasazení do funkcí Azure pomocí modulu plug-in Jenkins Azure Functions

[Azure Functions](/azure/azure-functions/) je výpočetní služba bez serveru. Pomocí Funkce Azure můžete spouštět kód na vyžádání bez zřizování nebo správy infrastruktury. Tento kurz ukazuje, jak nasadit funkci Java do funkce Azure pomocí modulu plug-in Azure Functions.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- **Server Jenkins**: Pokud nemáte nainstalovaný server Jenkins, přečtěte si článek [Vytvoření serveru Jenkins v Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > Zdrojový kód použitý pro tento kurz je umístěn v [úložišti Visual Studio China GitHub](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Vytvoření funkce Java

Chcete-li vytvořit funkci Java s runtime zásobníku Java, použijte buď [portál Azure](https://portal.azure.com) nebo [Azure CLI](/cli/azure/?view=azure-cli-latest).

Následující kroky ukazují, jak vytvořit funkci Java pomocí azure CLI:

1. Vytvořte skupinu prostředků a nahraďte zástupný symbol ** &lt;resource_group>** názvem skupiny prostředků.

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. Vytvořte účet úložiště Azure a nahrazte zástupné symboly příslušnými hodnotami.
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Vytvořte aplikaci testovací funkce a nahrazujte zástupné symboly příslušnými hodnotami.

    ```azurecli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkins

Následující kroky vysvětlují, jak připravit server Jenkins:

1. Nasazení [serveru Jenkins](https://aka.ms/jenkins-on-azure) v Azure. Pokud ještě nemáte nainstalovanou instanci serveru Jenkins, článek [Vytvoření serveru Jenkins v Azure](./install-jenkins-solution-template.md) vás provede procesem.

1. Přihlaste se k instanci Jenkins pomocí SSH.

1. V instanci Jenkins nainstalujte maven pomocí následujícího příkazu:

    ```terminal
    sudo apt install -y maven
    ```

1. V instanci Jenkins nainstalujte [nástroje Azure Functions Core Tools](/azure/azure-functions/functions-run-local) vydáním následujících příkazů na terminálové mačkání:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Na řídicí panel Jenkins estalujte následující moduly plug-in:

    - Modul plug-in Funkce Azure
    - EnvInject Plug-in

1. Jenkins potřebuje k ověření a přístupu k prostředkům Azure instanční objekt Azure. Podrobné pokyny najdete ve [službě Deploy to Azure App Service.](./tutorial-jenkins-deploy-web-app-azure-app-service.md)

1. Pomocí instančního objektu Azure přidejte v Jenkinsu typ přihlašovacích údajů "Microsoft Azure Service Principal". Podívejte se na kurz [Nasazení do služby Azure App Service.](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins)

## <a name="fork-the-sample-github-repo"></a>Rozvláčnost ukázkového úložiště GitHub

1. [Přihlaste se k úložišti GitHub pro lichou nebo dokonce ukázkovou aplikaci](https://github.com/VSChina/odd-or-even-function.git).

1. V pravém horním rohu v GitHubu zvolte **Fork**.

1. Podle pokynů vyberte svůj účet GitHub a dokončete rukopis.

## <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu Jenkins

V této části vytvoříte [jenkinsův kanál](https://jenkins.io/doc/book/pipeline/).

1. Na řídicím panelu Jenkinsvytvořte kanál.

1. Povolit **Připravit prostředí pro spuštění**.

1. Do **obsahu vlastností**přidejte následující proměnné prostředí a nahraďte zástupné symboly odpovídajícími hodnotami pro vaše prostředí:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. V části **Pipeline->Definition** vyberte **Pipeline Script from SCM**.

1. Zadejte adresu URL a cestu skriptu vaší fork githubu ("doc/resources/jenkins/JenkinsFile"), která se použije v [příkladu JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

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

## <a name="build-and-deploy"></a>Sestavení a nasazení

Teď je čas spustit Jenkinsovu úlohu.

1. Nejprve získat klíč autorizace prostřednictvím pokynů v azure [funkce HTTP aktivační události a vazby](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys) článku.

1. V prohlížeči zadejte adresu URL aplikace. Nahraďte zástupné symboly příslušnými hodnotami ** &lt;** a zadejte číselnou hodnotu pro input_number>jako vstup pro funkci Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Zobrazí se výsledky podobné následujícímu příkladu výstupu (kde liché číslo - 365 - byl použit jako test):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte prostředky, které jste vytvořili v následujícím kroku:

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Další kroky

Další informace o funkcích Azure najdete v následujícím zdroji:
> [!div class="nextstepaction"]
> [Dokumentace ke službě Azure Functions](/azure/azure-functions/)
