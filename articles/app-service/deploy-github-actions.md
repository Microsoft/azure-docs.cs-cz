---
title: Konfigurace CI/CD s akcemi GitHubu
description: Zjistěte, jak nasadit kód do služby Azure App Service z kanálu CI/CD pomocí akcí GitHubu. Přizpůsobte úlohy sestavení a spusťte komplexní nasazení.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084987"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Nasazení do služby App Service pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám poskytují flexibilitu při vytváření automatizovaného pracovního cyklu životního cyklu vývoje softwaru. S akcemi služby Azure App Service pro GitHub můžete automatizovat svůj pracovní postup a nasadit se do [služby Azure App Service](overview.md) pomocí akcí GitHub.

> [!IMPORTANT]
> Akce GitHubu jsou v současné době v beta verzi. Musíte [se nejprve zaregistrovat a připojit se k náhledu](https://github.com/features/actions) pomocí svého účtu GitHub.
> 

Pracovní postup je definován souborem YAML (.yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup služby Azure App Service má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definovat instanční objekt <br /> 2. Vytvoření tajného klíče GitHubu |
|**Sestavení** | 1. Nastavení životního prostředí <br /> 2. Vytvoření webové aplikace |
|**Nasadit** | 1. Nasazení webové aplikace |

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v [příkazovém příkazu Azure CLI](https://docs.microsoft.com/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) na webu Azure Portal nebo výběrem tlačítka Try **It.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

V tomto příkladu nahraďte zástupné symboly v prostředku ID předplatného, názvem skupiny prostředků a názvem aplikace. Výstup je pověření přiřazení role, které poskytují přístup k aplikaci App Service. Zkopírujte tento objekt JSON, který můžete použít k ověření z GitHubu.

> [!NOTE]
> Pokud se rozhodnete použít profil publikování pro ověřování, není nutné vytvářet instanční objekt.

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. To je důvod, proč obor v předchozím příkladu je omezena na konkrétní aplikaci služby App Service a ne celou skupinu prostředků.

## <a name="configure-the-github-secret"></a>Konfigurace tajného klíče GitHubu

Můžete také použít přihlašovací údaje na úrovni aplikace, tj. Postupujte podle pokynů pro konfiguraci tajného klíče:

1. Stáhněte si profil publikování aplikace App Service z portálu pomocí možnosti **Získat profil publikování.**

2. V [GitHubu](https://github.com/), procházejte úložiště, vyberte **Nastavení > Tajemství > Přidat nový tajný klíč**

    ![Tajemství](media/app-service-github-actions/secrets.png)

3. Vložte obsah staženého souboru profilu publikování do pole hodnoty tajného klíče.

4. Nyní v souboru pracovního `.github/workflows/workflow.yml` postupu ve vaší `publish-profile` větvi: nahraďte tajný klíč pro vstup akce nasazení Azure Web Appu.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Vidíte tajemství, jak je uvedeno níže, jakmile je definováno.

    ![Tajemství](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Nastavení prostředí

Nastavení prostředí lze provést pomocí jedné z akcí nastavení.

|**Jazyk**  |**Akce instalace**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Následující příklady ukazují část pracovního postupu, která nastavuje prostředí pro různé podporované jazyky:

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

## <a name="build-the-web-app"></a>Vytvoření webové aplikace

To závisí na jazyku a pro jazyky podporované službou Azure App Service, tato část by měla být standardní kroky sestavení každého jazyka.

Následující příklady ukazují část pracovního postupu, který vytváří webovou aplikaci v různých podporovaných jazycích.

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

Pokud chcete nasadit kód do aplikace `azure/webapps-deploy@v2` App Service, použijte akci. Tato akce má čtyři parametry:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | (Povinné) Název aplikace App Service | 
| **profil publikování** | (Nepovinné) Publikování obsahu souboru profilu pomocí tajných klíčů nasazení webu |
| **Balíček** | (Nepovinné) Cesta k balíčku nebo složce. *.zip, *.war, *.jar nebo složku k nasazení |
| **název slotu** | (Nepovinné) Zadejte existující slot jiný než produkční slot |

### <a name="deploy-using-publish-profile"></a>Nasazení pomocí profilu publikování

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node.js do Azure pomocí profilu publikování.

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

### <a name="deploy-using-azure-service-principal"></a>Nasazení pomocí instančního objektu služby Azure

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node.js do Azure pomocí hlavního povinného kontu Azure.

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

Můžete najít naši sadu akcí seskupených do různých úložišť na GitHubu, z nichž každá obsahuje dokumentaci a příklady, které vám pomohou používat GitHub pro CI/CD a nasadit aplikace do Azure.

- [Pracovní postup akcí pro nasazení do Azure](https://github.com/Azure/actions-workflow-samples)

- [Přihlášení k Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp pro kontejnery](https://github.com/Azure/webapps-container-deploy)

- [Přihlášení/odhlášení dockeru](https://github.com/Azure/docker-login)

- [Události, které aktivují pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s nasadit](https://github.com/Azure/k8s-deploy)

- [Počáteční pracovní postupy](https://github.com/actions/starter-workflows)
