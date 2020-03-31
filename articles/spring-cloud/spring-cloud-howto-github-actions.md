---
title: Azure Spring Cloud CI/CD s akcemi GitHubu
description: Jak vytvořit pracovní postup CI/CD pro Azure Spring Cloud s akcemi GitHubu
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538460"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD s akcemi GitHubu

GitHub Actions podporuje automatizovaný pracovní postup životního cyklu vývoje softwaru. Pomocí akce GitHubu pro Azure Spring Cloud můžete ve svém úložišti vytvářet pracovní postupy pro vytváření, testování, balení, vydávání a nasazení do Azure. 

## <a name="prerequisites"></a>Požadavky
Tento příklad vyžaduje [azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Nastavení úložiště GitHubu a ověření
K autorizaci akce přihlášení do Azure potřebujete přihlašovací údaje na principu služby Azure. Chcete-li získat pověření Azure, proveďte v místním počítači následující příkazy:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Chcete-li získat přístup k určité skupině prostředků, můžete zmenšit obor:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Příkaz by měl výstup json objektu:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Tento příklad používá ukázku [Metriky prasátka](https://github.com/Azure-Samples/piggymetrics) na GitHubu.  Rozvláhněte ukázku, otevřete stránku úložiště **Secrets** GitHub a **Add a new secret**klikněte na kartu **Nastavení.**

 ![Přidat nový tajný klíč](./media/github-actions/actions1.png)

Nastavte název tajného klíče na `AZURE_CREDENTIALS` a jeho hodnotu na řetězec JSON, který jste našli pod nadpisem Nastavte úložiště *GitHub a ověřte*.

 ![Nastavení tajných dat](./media/github-actions/actions2.png)

Přihlašovací údaje Azure můžete také získat z key vaultu v akcích GitHubu, jak je vysvětleno v [tématu Authenticate Azure Spring with Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Instance zřizování služeb
Chcete-li zřídit instanci služby Azure Spring Cloud, spusťte následující příkazy pomocí příkazového příkazu Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Vytvoření pracovního postupu
Pracovní postup je definován pomocí následujících možností.

### <a name="prepare-for-deployment-with-azure-cli"></a>Příprava na nasazení pomocí azure cli
Příkaz `az spring-cloud app create` není v současné době idempotentní.  Tento pracovní postup doporučujeme na stávajících aplikacích a instancích Azure Spring Cloud.

Pro přípravu použijte následující příkazy azure CLI:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Nasazení pomocí azure cli přímo
Vytvořte `.github/workflow/main.yml` soubor v úložišti:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Nasazení pomocí akce Azure CLI
Příkaz az `run` bude používat nejnovější verzi příkazového příkazu Azure CLI. Pokud dojde k narušující změny, můžete také použít konkrétní verzi `action`Azure CLI s azure/CLI . 

> [!Note] 
> Tento příkaz bude spuštěn v `env` novém kontejneru, takže nebude fungovat a přístup k souborům s křížovou akcí může mít další omezení.

Vytvořte soubor .github/workflow/main.yml v úložišti:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Nasazení pomocí modulu plug-in Maven
Další možností je použití [modulu plug-in Maven](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) pro nasazení jar a aktualizaci nastavení aplikace. Příkaz `mvn azure-spring-cloud:deploy` je idempotentní a v případě potřeby automaticky vytvoří aplikace. Nemusíte vytvářet odpovídající aplikace předem.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Spuštění pracovního postupu
Akce **GitHubu** by měly `.github/workflow/main.yml` být povoleny automaticky po přepnutí na GitHub. Akce se spustí při nabízení nové revize. Pokud tento soubor vytvoříte v prohlížeči, měla by být akce již spuštěna.

Chcete-li ověřit, že byla akce povolena, klikněte na stránce úložiště GitHub na kartu **Akce:**

 ![Ověřit povolenou akci](./media/github-actions/actions3.png)

Pokud vaše akce běží omylem, například pokud jste nenastavili pověření Azure, můžete znovu spustit kontroly po opravě chyby. Na stránce úložiště GitHub klikněte na **Akce**, vyberte konkrétní úlohu pracovního postupu a potom kliknutím na tlačítko **Znovu spustit kontroly** znovu spusťte kontroly:

 ![Znovu spustit kontroly](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Další kroky
* [Key Vault pro akce GitHub spring cloudu](./spring-cloud-github-actions-key-vault.md)
* [Objekty služby Azure Active Directory](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Akce GitHubu pro Azure](https://github.com/Azure/actions/)
