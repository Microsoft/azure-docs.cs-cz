---
title: Nasazení do Azure Functions pomocí modulu plug-in pro Azure Functions Jenkinse
description: Naučte se, jak nasadit Azure Functions pomocí modulu plug-in Azure Functions Jenkinse
ms.service: jenkins
keywords: Jenkinse, Azure, DevOps, Java, Azure Functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 58267c607b0c4f2eaaf242c8e0752451f8c04c9a
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882038"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Nasazení do Azure Functions pomocí modulu plug-in pro Azure Functions Jenkinse

[Azure Functions](/azure/azure-functions/) je služba COMPUTE bez serveru. Pomocí Azure Functions můžete spustit kód na vyžádání bez zřízení nebo správy infrastruktury. V tomto kurzu se dozvíte, jak nasadit funkci Java, která Azure Functions pomocí modulu plug-in Azure Functions.

## <a name="prerequisites"></a>Předpoklady

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- **Server Jenkinse**: Pokud nemáte nainstalovaný server Jenkinse, přečtěte si článek [vytvoření serveru Jenkinse v Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > Zdrojový kód, který se používá pro tento kurz, se nachází v [úložišti GitHub sady Visual Studio Čína](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Vytvoření funkce Java

Chcete-li vytvořit funkci Java pomocí zásobníku Java Runtime, použijte buď [Azure Portal](https://portal.azure.com) , nebo rozhraní příkazového [řádku Azure](/cli/azure/?view=azure-cli-latest).

Následující kroky ukazují, jak vytvořit funkci Java pomocí rozhraní příkazového řádku Azure:

1. Vytvořte skupinu prostředků a nahraďte zástupný text **&lt;resource_group >** názvem vaší skupiny prostředků.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Vytvořte účet úložiště Azure a nahraďte zástupné symboly příslušnými hodnotami.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Vytvořte aplikaci funkcí testu a nahraďte zástupné symboly příslušnými hodnotami.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkinse

Následující postup vysvětluje, jak připravit server Jenkinse:

1. Nasazení [serveru Jenkinse](https://aka.ms/jenkins-on-azure) v Azure. Pokud ještě nemáte nainstalovanou instanci serveru Jenkinse, provede vás celým procesem [vytvoření serveru Jenkinse v Azure](./install-jenkins-solution-template.md) .

1. Přihlaste se k instanci Jenkinse pomocí SSH.

1. V instanci Jenkinse nainstalujte Maven pomocí následujícího příkazu:

    ```terminal
    sudo apt install -y maven
    ```

1. V instanci Jenkinse nainstalujte [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) , a to tak, že na příkazovém řádku zadáte následující příkazy:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Na řídicím panelu Jenkinse nainstalujte následující moduly plug-in:

    - Modul plug-in Azure Functions
    - Modul plug-in EnvInject

1. Pro ověření a přístup k prostředkům Azure potřebuje Jenkinse instanční objekt Azure. Podrobné pokyny najdete v tématu věnovaném [nasazení na Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) .

1. Pomocí instančního objektu Azure přidejte do Jenkinse typ přihlašovacích údajů "Microsoft Azure instančního objektu". Přečtěte si kurz [nasazení na Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) .

## <a name="fork-the-sample-github-repo"></a>Rozvětvení ukázkového úložiště GitHub

1. [Přihlaste se k úložišti GitHub pro vzorovou nebo stejnou ukázkovou aplikaci](https://github.com/VSChina/odd-or-even-function.git).

1. V pravém horním rohu GitHubu vyberte možnost **rozvětvení**.

1. Podle pokynů vyberte svůj účet GitHub a dokončete rozvětvení.

## <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu Jenkinse

V této části vytvoříte [kanál Jenkinse](https://jenkins.io/doc/book/pipeline/).

1. V řídicím panelu Jenkinse vytvořte kanál.

1. Povolte **přípravu prostředí pro běh**.

1. Přidejte následující proměnné prostředí do **obsahu vlastnosti**a nahraďte zástupné symboly odpovídajícími hodnotami pro vaše prostředí:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. V části **> kanálu – definice** vyberte **skript kanálu z SCM**.

1. Zadejte adresu URL a cestu ke svému rozvětvení GitHubu ("doc/Resources/Jenkinse/JenkinsFile"), abyste je mohli použít v [příkladu JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

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

Teď je čas spustit úlohu Jenkinse.

1. Nejdřív Získejte autorizační klíč pomocí pokynů v článku [Azure Functions aktivační události a vazby protokolu HTTP](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) .

1. V prohlížeči zadejte adresu URL aplikace. Zástupné symboly nahraďte odpovídajícími hodnotami a zadejte číselnou hodnotu pro **&lt;input_number >** jako vstup pro funkci Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Výsledky jsou podobné jako v následujícím příkladu výstupu (kde bylo použito liché číslo-365 – bylo použito jako test):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte prostředky, které jste vytvořili, pomocí následujícího kroku:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions najdete v následujících zdrojích:
> [!div class="nextstepaction"]
> [Dokumentace k Azure Functions](/azure/azure-functions/)
