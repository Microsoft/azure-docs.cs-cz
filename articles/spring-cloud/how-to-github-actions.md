---
title: Azure jarní Cloud CI/CD s akcemi GitHubu
description: Postup sestavení pracovního postupu CI/CD pro jarní Cloud v Azure s akcemi GitHubu
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c52279108a8fd8d5a7ac8bbd7c8eb215097b21b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791350"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure jarní Cloud CI/CD s akcemi GitHubu

Akce GitHubu podporují automatizovaný pracovní postup pro životní cyklus vývoje softwaru. S akcemi GitHubu pro jarní cloud Azure můžete vytvářet pracovní postupy v úložišti pro vytváření, testování, balení, vydávání a nasazování do Azure. 

## <a name="prerequisites"></a>Požadavky
Tento příklad vyžaduje rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Nastavení úložiště GitHub a ověření
K autorizaci akce přihlášení Azure budete potřebovat přihlašovací údaje instančního objektu Azure. Pokud chcete získat přihlašovací údaje Azure, spusťte na svém místním počítači tyto příkazy:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Pro přístup ke konkrétní skupině prostředků můžete omezit rozsah:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Příkaz by měl mít výstup objektu JSON:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

V tomto příkladu se používá [Ukázka steeltoe na GitHubu](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Rozvětvení úložiště, otevřete stránku úložiště GitHub pro rozvětvení a vyberte kartu **Nastavení** . Otevřete nabídku **tajné klíče** a vyberte **nový tajný kód**:

 ![Přidat nový tajný klíč](./media/github-actions/actions1.png)

Nastavte název tajného klíče na `AZURE_CREDENTIALS` a jeho hodnotu na řetězec JSON, který jste našli pod nadpisem *Nastavení úložiště GitHub a ověření*.

 ![Nastavení tajných dat](./media/github-actions/actions2.png)

Přihlašovací údaje Azure můžete taky získat z Key Vault v akcích GitHubu, jak je vysvětleno v tématu [ověřování Azure jaře pomocí Key Vault v akcích GitHubu](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Zřídit instanci služby
Pokud chcete zřídit instanci služby jarní cloudovou službu Azure, spusťte následující příkazy pomocí Azure CLI.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Sestavení pracovního postupu
Pracovní postup je definován pomocí následujících možností.

### <a name="prepare-for-deployment-with-azure-cli"></a>Příprava nasazení pomocí Azure CLI

Příkaz `az spring-cloud app create` se momentálně neidempotentní. Po spuštění tohoto příkazu se zobrazí chyba, pokud znovu spustíte stejný příkaz. Tento pracovní postup doporučujeme pro existující cloudové aplikace a instance Azure jaře.

Pro přípravu použijte následující příkazy rozhraní příkazového řádku Azure:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Přímé nasazení pomocí Azure CLI

`.github/workflows/main.yml`V úložišti vytvořte soubor s následujícím obsahem. Nahraďte `<your resource group name>` a `<your service name>` správnými hodnotami.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>Nastavení úložiště GitHub a ověření
K autorizaci akce přihlášení Azure budete potřebovat přihlašovací údaje instančního objektu Azure. Pokud chcete získat přihlašovací údaje Azure, spusťte na svém místním počítači tyto příkazy:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Pro přístup ke konkrétní skupině prostředků můžete omezit rozsah:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Příkaz by měl mít výstup objektu JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

V tomto příkladu se používá ukázka [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) na GitHubu.  Rozvětvete ukázku a otevřete stránku úložiště GitHub a klikněte na kartu **Nastavení** . Otevřete nabídku **tajné klíče** a klikněte na **Přidat nový tajný kód**:

 ![Přidat nový tajný klíč](./media/github-actions/actions1.png)

Nastavte název tajného klíče na `AZURE_CREDENTIALS` a jeho hodnotu na řetězec JSON, který jste našli pod nadpisem *Nastavení úložiště GitHub a ověření*.

 ![Nastavení tajných dat](./media/github-actions/actions2.png)

Přihlašovací údaje Azure můžete taky získat z Key Vault v akcích GitHubu, jak je vysvětleno v tématu [ověřování Azure jaře pomocí Key Vault v akcích GitHubu](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Zřídit instanci služby
Pokud chcete zřídit instanci služby jarní cloudovou službu Azure, spusťte následující příkazy pomocí Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Sestavení pracovního postupu
Pracovní postup je definován pomocí následujících možností.

### <a name="prepare-for-deployment-with-azure-cli"></a>Příprava nasazení pomocí Azure CLI
Příkaz `az spring-cloud app create` se momentálně neidempotentní.  Tento pracovní postup doporučujeme pro existující cloudové aplikace a instance Azure jaře.

Pro přípravu použijte následující příkazy rozhraní příkazového řádku Azure:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Přímé nasazení pomocí Azure CLI
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
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
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
### <a name="deploy-with-azure-cli-action"></a>Akce nasazení pomocí Azure CLI
Příkaz AZ použije `run` nejnovější verzi rozhraní příkazového řádku Azure CLI. Pokud dojde k zásadním změnám, můžete použít také konkrétní verzi rozhraní příkazového řádku Azure CLI s Azure/CLI `action` . 

> [!Note] 
> Tento příkaz se spustí v novém kontejneru, takže nebude `env` fungovat a přístup k souborům mezi akcemi může mít navíc omezení.

V úložišti vytvořte soubor. GitHub/Workflow/Main. yml:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
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

## <a name="deploy-with-maven-plugin"></a>Nasazení s modulem plug-in Maven
Další možností je použít [modul plug-in Maven](./spring-cloud-quickstart.md) k nasazení jar a aktualizaci nastavení aplikace. Příkaz `mvn azure-spring-cloud:deploy` je idempotentní a v případě potřeby vytvoří aplikace automaticky. Nemusíte vytvářet odpovídající aplikace předem.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
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

::: zone-end

## <a name="run-the-workflow"></a>Spustit pracovní postup

**Akce** GitHubu by se měly povolit automaticky po vložení `.github/workflow/main.yml` do GitHubu. Akce bude aktivována při vložení nového potvrzení změn. Pokud tento soubor vytvoříte v prohlížeči, měla by být akce již spuštěna.

Chcete-li ověřit, zda byla akce povolena, klikněte na kartu **Akce** na stránce úložiště GitHub:

![Ověřit povolenou akci](./media/github-actions/actions3.png)

Pokud se vaše akce spouští jako chyba, například pokud jste nenastavili přihlašovací údaje Azure, můžete po opravě chyby znovu spustit kontrolu. Na stránce úložiště GitHub klikněte na **Akce**, vyberte úlohu konkrétního pracovního postupu a potom kliknutím na tlačítko **znovu spustit kontroly** znovu spusťte kontrolu:

![Znovu spustit kontrolu](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Další kroky

* [Key Vault pro jarní cloudové akce GitHubu](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory instančních objektů](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [Akce GitHubu pro Azure](https://github.com/Azure/actions/)
