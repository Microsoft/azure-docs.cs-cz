---
title: Konfigurace CI/CD s akcemi GitHubu
description: Naučte se, jak nasadit kód pro Azure App Service z kanálu CI/CD s akcemi GitHubu. Přizpůsobte úkoly sestavení a proveďte složitá nasazení.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: be6b5f0af17aa8343dcb74fd5f0710d44332ce0e
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193295"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Nasazení do App Service pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. Díky akcím Azure App Service pro GitHub můžete automatizovat svůj pracovní postup nasazení do [Azure App Service](overview.md) pomocí akcí GitHubu.

> [!IMPORTANT]
> Akce GitHubu jsou momentálně ve verzi beta. [Abyste se mohli připojit ke službě Preview](https://github.com/features/actions) pomocí svého účtu GitHubu, musíte se nejdřív zaregistrovat.
> 

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

V případě pracovního postupu Azure App Service má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. definování instančního objektu <br /> 2. vytvoření tajného kódu GitHubu |
|**Sestavení** | 1. nastavení prostředí <br /> 2. sestavení webové aplikace |
|**Nasazení** | 1. nasazení webové aplikace |

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

V tomto příkladu Nahraďte zástupné symboly v prostředku ID vašeho předplatného, název skupiny prostředků a název aplikace. Výstupem jsou přihlašovací údaje přiřazení role, které poskytují přístup k vaší aplikaci App Service. Zkopírujte tento objekt JSON, který můžete použít k ověření z GitHubu.

> [!NOTE]
> Pokud se rozhodnete použít profil publikování pro ověřování, nemusíte vytvářet instanční objekt.

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. To je důvod, proč je obor v předchozím příkladu omezený na konkrétní App Service aplikaci, a ne na celou skupinu prostředků.

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

Můžete také použít přihlašovací údaje na úrovni aplikace, tj. profil publikování pro nasazení. Použijte postup konfigurace tajného klíče:

1. Stáhněte si profil publikování pro aplikaci App Service z portálu pomocí možnosti **získat profil publikování** .

2. V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných klíčů > přidat nový tajný kód** .

    ![záleží](media/app-service-github-actions/secrets.png)

3. Do pole hodnota tajného klíče vložte obsah pro stažený soubor publikačního profilu.

4. Nyní v souboru pracovního postupu ve větvi: `.github/workflows/workflow.yml` nahraďte tajný klíč pro vstup `publish-profile` akce nasazení webové aplikace Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Po definování se zobrazí tajný klíč, jak je znázorněno níže.

    ![záleží](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Nastavení prostředí

Nastavení prostředí je možné provést pomocí jedné z akcí nastavení.

|**Jazyk**  |**Akce nastavení**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Následující příklady znázorňují část pracovního postupu, která nastavuje prostředí pro různé podporované jazyky:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Sestavení webové aplikace

To závisí na jazyku a jazycích podporovaných nástrojem Azure App Service, Tato část by měla být standardním postupem sestavení každého jazyka.

Následující příklady znázorňují část pracovního postupu, který vytváří webovou aplikaci v různých podporovaných jazycích.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Nasazení do App Service

K nasazení kódu do aplikace App Service použijte `azure/webapps-deploy@v2` akci. Tato akce má čtyři parametry:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | Požadovanou Název aplikace App Service | 
| **publikování – profil** | Volitelné Publikování obsahu souboru profilu pomocí Nasazení webu tajných klíčů |
| **balíček** | Volitelné Cesta k balíčku nebo složce *. zip, *. War, *. jar nebo složka, která se má nasadit |
| **název slotu** | Volitelné Zadejte jinou existující patici, než je produkční slot. |

### <a name="deploy-using-publish-profile"></a>Nasadit pomocí profilu publikování

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node. js do Azure pomocí publikačního profilu.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Nasazení s použitím instančního objektu Azure

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node. js do Azure pomocí instančního objektu Azure.

```yaml
on: [push]

name: Node.js

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
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Další kroky

Můžete najít naši sadu akcí seskupených do různých úložišť na GitHubu. Každá z nich obsahuje dokumentaci a příklady, které vám pomůžou používat GitHub pro CI/CD a nasazovat aplikace do Azure.

- [Pracovní postup akcí k nasazení do Azure](https://github.com/Azure/actions-workflow-samples)

- [Přihlášení k Azure](https://github.com/Azure/login)

- [WebApp Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp pro kontejnery](https://github.com/Azure/webapps-container-deploy)

- [Přihlášení nebo odhlášení Docker](https://github.com/Azure/docker-login)

- [Události, které spouštějí pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Nasazení K8s](https://github.com/Azure/k8s-deploy)

- [Úvodní pracovní postupy](https://github.com/actions/starter-workflows)
