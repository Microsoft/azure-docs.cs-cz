---
title: Konfigurace CI/CD s akcemi GitHubu
description: Naučte se, jak nasadit kód pro Azure App Service z kanálu CI/CD s akcemi GitHubu. Přizpůsobte úkoly sestavení a proveďte složitá nasazení.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: e2432ca4cecb3c36d2fae19907c1ad17d9ef2505
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833499"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Nasazení do App Service pomocí akcí GitHubu

Začněte s [akcemi GitHubu](https://help.github.com/en/articles/about-github-actions) a Automatizujte pracovní postup a nasaďte ho do [Azure App Service](overview.md) z GitHubu. 

## <a name="prerequisites"></a>Předpoklady 

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).  
- Pracovní aplikace Azure App Service. 
    - .NET: [Vytvoření webové aplikace v ASP.NET Core v Azure](quickstart-dotnetcore.md)
    - ASP.NET: [Vytvoření webové aplikace ASP.NET Framework v Azure](quickstart-dotnet-framework.md)
    - JavaScript: [Vytvoření webové aplikace v Node.js v Azure App Service](quickstart-nodejs.md)  
    - Java: [Vytvoření aplikace Java v Azure App Service](quickstart-java.md)
    - Python: [Vytvoření aplikace v Pythonu v Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor má tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt nebo publikační profil. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Sestavení** | 1. Nastavte prostředí. <br /> 2. Sestavte webovou aplikaci. |
|**Nasazení** | 1. Nasaďte webovou aplikaci. |

## <a name="use-the-deployment-center"></a>Použití centra nasazení

Pomocí centra nasazení App Service můžete rychle začít s akcemi GitHubu. Tím se automaticky vygeneruje soubor pracovního postupu na základě vašeho zásobníku aplikace a potvrdí se do úložiště GitHub ve správném adresáři.

1. Přejděte do svého webappu v Azure Portal
1. Na levé straně klikněte na **centrum nasazení** .
1. V části **průběžné nasazování (CI/CD)** vyberte **GitHub** .
1. Pak vyberte **Akce GitHubu** .
1. Výběr úložiště GitHubu, větve a zásobníku aplikací pomocí rozevíracích seznamů
    - Pokud je vybraná větev chráněná, můžete i nadále přidávat soubor pracovního postupu. Než budete pokračovat, nezapomeňte zkontrolovat ochranu větví.
1. Na poslední obrazovce si můžete prohlédnout své volby a zobrazit náhled souboru pracovního postupu, který se potvrdí do úložiště. Pokud jsou výběry správné, klikněte na **Dokončit** .

Tím se soubor pracovního postupu potvrdí do úložiště. Spustí se okamžitě pracovní postup sestavení a nasazení vaší aplikace.

## <a name="set-up-a-work-manually"></a>Ruční nastavení práce

Pracovní postup můžete nasadit i bez použití centra nasazení. K tomu budete muset nejdřív vygenerovat přihlašovací údaje pro nasazení. 

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

Doporučený způsob ověřování pomocí Azure App Services pro akce GitHubu je profil publikování. Můžete se také ověřit pomocí instančního objektu, ale proces vyžaduje více kroků. 

Uložte přihlašovací údaje k publikačnímu profilu nebo instančnímu objektu jako [tajný kód GitHubu](https://docs.github.com/en/actions/reference/encrypted-secrets) pro ověření v Azure. V rámci pracovního postupu budete mít přístup ke tajnému kódu. 

# <a name="publish-profile"></a>[Publikovat profil](#tab/applevel)

Profil publikování je přihlašovací údaje na úrovni aplikace. Nastavte svůj publikační profil jako tajný klíč GitHubu. 

1. V Azure Portal přejdete do služby App Service. 

1. Na stránce **Přehled** vyberte **získat profil publikování**.

1. Stažený soubor uložte. Pomocí obsahu souboru vytvoříte tajný klíč GitHubu.

> [!NOTE]
> Od října 2020 budou webové aplikace pro Linux potřebovat nastavení aplikace `WEBSITE_WEBDEPLOY_USE_SCM` nastavené na hodnotu `true` **před stažením publikačního profilu**. Tento požadavek se v budoucnu odebere.

# <a name="service-principal"></a>[Instanční objekt](#tab/userlevel)

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

V předchozím příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného, názvem skupiny prostředků a názvem aplikace. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší App Service aplikaci, která je podobná níže. Zkopírujte tento objekt JSON pro pozdější verzi.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. Obor v předchozím příkladu je omezený na konkrétní App Service aplikaci, a ne na celou skupinu prostředků.

---

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu


# <a name="publish-profile"></a>[Publikovat profil](#tab/applevel)

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

Pokud chcete použít [přihlašovací údaje na úrovni aplikace](#generate-deployment-credentials), vložte obsah staženého souboru publikačního profilu do pole hodnota tajného klíče. Pojmenujte tajný klíč `AZURE_WEBAPP_PUBLISH_PROFILE` .

Když nakonfigurujete pracovní postup GitHubu, použijte `AZURE_WEBAPP_PUBLISH_PROFILE` v akci nasazení webové aplikace Azure. Například:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Instanční objekt](#tab/userlevel)

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

Pokud chcete použít [přihlašovací údaje na úrovni uživatele](#generate-deployment-credentials), vložte celý výstup JSON z příkazu Azure CLI do pole hodnota tajného klíče. Zadejte název tajného klíče `AZURE_CREDENTIALS` .

Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `creds` Akce přihlášení do Azure. Například:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Nastavení prostředí

Nastavení prostředí je možné provést pomocí jedné z akcí nastavení.

|**Jazyk**  |**Akce nastavení**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Následující příklady ukazují, jak nastavit prostředí pro různé podporované jazyky:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Sestavení webové aplikace

Proces sestavení webové aplikace a nasazení pro Azure App Service změny v závislosti na jazyku. 

Následující příklady znázorňují část pracovního postupu, která vytváří webovou aplikaci v různých podporovaných jazycích.

Pro všechny jazyky můžete nastavit kořenový adresář webové aplikace pomocí `working-directory` . 

**.NET**

Proměnná prostředí `AZURE_WEBAPP_PACKAGE_PATH` nastaví cestu k vašemu projektu webové aplikace. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Můžete obnovit závislosti NuGet a spustit MSBuild pomocí `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Pro Node.js můžete nastavit `working-directory` nebo změnit adresář npm v nástroji `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Nasazení do App Service

K nasazení kódu do aplikace App Service použijte `azure/webapps-deploy@v2` akci. Tato akce má čtyři parametry:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | Požadovanou Název aplikace App Service | 
| **publikování – profil** | Volitelné Publikování obsahu souboru profilu pomocí Nasazení webu tajných klíčů |
| **balíček** | Volitelné Cesta k balíčku nebo složce Cesta může zahrnovat *. zip, *. War, *. jar nebo složku pro nasazení. |
| **název slotu** | Volitelné Zadejte jinou existující patici, než je produkční [slot](deploy-staging-slots.md) . |


# <a name="publish-profile"></a>[Publikovat profil](#tab/applevel)

### <a name="net-core"></a>.NET Core

Sestavte a nasaďte aplikaci .NET Core do Azure pomocí profilu publikování v Azure. `publish-profile`Vstup odkazuje na `AZURE_WEBAPP_PUBLISH_PROFILE` tajný klíč, který jste vytvořili dříve.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Sestavte a nasaďte aplikaci ASP.NET MVC, která používá NuGet a `publish-profile` pro ověřování. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Sestavte a nasaďte aplikaci s pružinou v jazyce Java do Azure pomocí profilu publikování v Azure. `publish-profile`Vstup odkazuje na `AZURE_WEBAPP_PUBLISH_PROFILE` tajný klíč, který jste vytvořili dříve.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Chcete-li nasadit `war` místo a `jar` , změňte `package` hodnotu. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Sestavte a nasaďte aplikaci Node.js do Azure pomocí profilu publikování aplikace. `publish-profile`Vstup odkazuje na `AZURE_WEBAPP_PUBLISH_PROFILE` tajný klíč, který jste vytvořili dříve.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Sestavte a nasaďte aplikaci v Pythonu do Azure pomocí profilu publikování aplikace. Všimněte si `publish-profile` , jak vstup odkazuje na `AZURE_WEBAPP_PUBLISH_PROFILE` tajný kód, který jste vytvořili dříve.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Instanční objekt](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Sestavte a nasaďte aplikaci .NET Core do Azure pomocí instančního objektu Azure. Všimněte si `creds` , jak vstup odkazuje na `AZURE_CREDENTIALS` tajný kód, který jste vytvořili dříve.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@master
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Sestavte a nasaďte aplikaci ASP.NET MVC do Azure pomocí instančního objektu Azure. Všimněte si `creds` , jak vstup odkazuje na `AZURE_CREDENTIALS` tajný kód, který jste vytvořili dříve.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@master  
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Sestavte a nasaďte aplikaci s pružinou v jazyce Java do Azure pomocí instančního objektu Azure. Všimněte si `creds` , jak vstup odkazuje na `AZURE_CREDENTIALS` tajný kód, který jste vytvořili dříve.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Sestavte a nasaďte aplikaci Node.js do Azure pomocí instančního objektu Azure. Všimněte si `creds` , jak vstup odkazuje na `AZURE_CREDENTIALS` tajný kód, který jste vytvořili dříve.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Sestavte a nasaďte aplikaci v Pythonu do Azure pomocí instančního objektu Azure. Všimněte si `creds` , jak vstup odkazuje na `AZURE_CREDENTIALS` tajný kód, který jste vytvořili dříve.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Další kroky

Můžete najít naši sadu akcí seskupených do různých úložišť na GitHubu. Každá z nich obsahuje dokumentaci a příklady, které vám pomůžou používat GitHub pro CI/CD a nasazovat aplikace do Azure.

- [Akce pracovních postupů k nasazení do Azure](https://github.com/Azure/actions-workflow-samples)

- [Přihlášení k Azure](https://github.com/Azure/login)

- [WebApp Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp pro kontejnery](https://github.com/Azure/webapps-container-deploy)

- [Přihlášení nebo odhlášení Docker](https://github.com/Azure/docker-login)

- [Události, které aktivují pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Nasazení K8s](https://github.com/Azure/k8s-deploy)

- [Úvodní pracovní postupy](https://github.com/actions/starter-workflows)
