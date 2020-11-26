---
title: Použití akcí GitHubu ke zpřístupnění aktualizací kódu v Azure Functions
description: Naučte se používat akce GitHubu k definování pracovního postupu pro sestavování a nasazování projektů Azure Functions v GitHubu.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: 651c1913491952c53af42abec5ce5d5009da06a0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168062"
---
# <a name="continuous-delivery-by-using-github-action"></a>Průběžné doručování pomocí akce GitHubu

Pomocí [akcí GitHubu](https://github.com/features/actions) Definujte pracovní postup, který automaticky sestaví a nasadí kód do vaší aplikace funkce Azure Functions. 

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) v akcích GitHub je automatizovaný proces, který definujete v úložišti GitHub. Tento proces oznamuje GitHubu, jak sestavit a nasadit projekt Function App na GitHubu. 

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup. 

V případě pracovního postupu Azure Functions má soubor tři části: 

| Sekce | Úlohy |
| ------- | ----- |
| **Authentication** | Stáhněte si profil publikování.<br/>Vytvořte tajný klíč GitHubu.|
| **Sestavení** | Nastavte prostředí.<br/>Sestavte aplikaci Function App.|
| **Nasazení** | Nasaďte aplikaci Function App.|

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).  
- Pracovní funkce hostovaná v Azure s úložištěm GitHub.   
    - [Rychlé zprovoznění: Vytvoření funkce v Azure s využitím Visual Studio Code](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

Doporučený způsob ověřování pomocí Azure Functions pro akce GitHubu je použití profilu publikování. Můžete se také ověřit pomocí instančního objektu. Další informace najdete v [tomto úložišti akcí GitHubu](https://github.com/Azure/functions-action). 

Po uložení přihlašovacích údajů profilu publikování jako [tajného kódu GitHubu](https://docs.github.com/en/actions/reference/encrypted-secrets)použijete tento tajný klíč v rámci pracovního postupu k ověření v Azure. 

#### <a name="download-your-publish-profile"></a>Stáhnout publikační profil

Chcete-li stáhnout profil publikování aplikace Function App:

1. Vyberte stránku **Přehled** aplikace Function App a pak vyberte **získat profil publikování**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Stáhnout profil publikování":::

1. Uložte a zkopírujte obsah souboru.


### <a name="add-the-github-secret"></a>Přidat tajný klíč GitHubu

1. V [GitHubu](https://github.com)přejděte do úložiště, vyberte **Nastavení**  >  **tajné klíče**  >  **Přidat nový tajný kód**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Přidat tajný kód":::

1. Přidejte nový tajný kód s `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` použitím **pro název**, obsah souboru profilu publikování pro **hodnotu** a pak vyberte **Přidat tajný klíč**.

GitHub se teď může ověřit pro vaši aplikaci Function App v Azure.

## <a name="create-the-environment"></a>Vytvoření prostředí 

Nastavení prostředí se provádí pomocí akce nastavení publikování pro konkrétní jazyk.

# <a name="net"></a>[.NET](#tab/dotnet)

Rozhraní .NET (včetně ASP.NET) používá `actions/setup-dotnet` akci.  
Následující příklad ukazuje část pracovního postupu, která nastavuje prostředí:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

Jazyk Java tuto  `actions/setup-java` akci používá.  
Následující příklad ukazuje část pracovního postupu, která nastavuje prostředí:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript (Node.js) používá `actions/setup-node` akci.  
Následující příklad ukazuje část pracovního postupu, která nastavuje prostředí:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

Python používá `actions/setup-python` akci.  
Následující příklad ukazuje část pracovního postupu, která nastavuje prostředí:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>Sestavení aplikace Function App

To závisí na jazyku a jazycích podporovaných nástrojem Azure Functions, Tato část by měla být standardním postupem sestavení každého jazyka.

Následující příklad ukazuje část pracovního postupu, který vytváří aplikaci Function App, která je specifická pro jazyk:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>Nasazení aplikace funkcí
Použijte `Azure/functions-action` akci pro nasazení kódu do aplikace Function App. Tato akce má tři parametry:

|Parametr |Vysvětlení  |
|---------|---------|
|_**název aplikace**_ | Závaznou Název vaší aplikace Function App |
|_**název slotu**_ | Volitelné Název [slotu nasazení](functions-deployment-slots.md) , na který chcete nasadit. Slot už musí být definovaný ve vaší aplikaci Function App. |
|_**publikování – profil**_ | Volitelné Název tajného kódu GitHubu pro váš publikační profil. |

Následující příklad používá `functions-action` pro ověřování verze 1 a. `publish profile` 

# <a name="net"></a>[.NET](#tab/dotnet)

Nastavte pracovní postup .NET Linux, který používá profil publikování.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Nastavte pracovní postup systému Windows .NET, který používá profil publikování.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Nastavte pracovní postup Java Linux, který používá profil publikování.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Nastavte pracovní postup Java Windows, který používá profil publikování.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nastavte pracovní postup pro Node.JS Linux, který používá profil publikování.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Nastavte Node.JS pracovní postup systému Windows, který používá profil publikování.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Nastavte pracovní postup pro Python Linux, který používá profil publikování.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@master

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o integraci Azure a GitHubu](/azure/developer/github/)