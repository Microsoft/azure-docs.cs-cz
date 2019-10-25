---
title: Nasazení kódu z kanálu CI/CD s akcemi na GitHubu – Azure App Service | Microsoft Docs
description: Naučte se používat akce GitHubu k nasazení kódu pro App Service
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: jafreebe
ms.openlocfilehash: b7ec1ae1d04fb1dbe16fd9f4a2640b2b3d9584c2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809770"
---
# <a name="github-actions-for-deploying-to-app-service"></a>Akce GitHubu pro nasazení do App Service

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. Díky akcím Azure App Service pro GitHub můžete automatizovat pracovní postup nasazení [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/) pomocí akcí GitHubu.

> [!IMPORTANT]
> Akce GitHubu jsou momentálně ve verzi beta. [Abyste se mohli připojit ke službě Preview](https://github.com/features/actions) pomocí svého účtu GitHubu, musíte se nejdřív zaregistrovat.
> 

Pracovní postup je definovaný souborem YAML (. yml) v cestě `/.github/workflows/` v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup webové aplikace Azure má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Ověřování** | 1. definování instančního objektu <br /> 2. vytvoření tajného kódu GitHubu |
|**Budování** | 1. nastavení prostředí <br /> 2. sestavení webové aplikace |
|**Nasazení** | 1. nasazení webové aplikace |

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

V tomto příkladu Nahraďte zástupné symboly v prostředku ID vašeho předplatného, skupiny prostředků a názvu webové aplikace. Výstupem jsou přihlašovací údaje přiřazení role, které poskytují přístup k vaší webové aplikaci. Zkopírujte tento objekt JSON, který můžete použít k ověření z GitHubu.

> [!NOTE]
> Pokud se rozhodnete použít profil publikování pro ověřování, nemusíte vytvářet instanční objekt.

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. To je důvod, proč je obor v předchozím příkladu omezen na konkrétní webovou aplikaci, nikoli na celou skupinu prostředků.

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

Můžete také použít přihlašovací údaje na úrovni aplikace, tj. profil publikování pro nasazení. Použijte postup konfigurace tajného klíče:

1. Stáhněte si profil publikování pro webovou aplikaci z portálu pomocí možnosti **získat profil publikování** .

2. V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných klíčů > Přidat nový tajný kód** .

    ![záleží](media/app-service-github-actions/secrets.png)

3. Do pole hodnota tajného klíče vložte obsah pro stažený soubor publikačního profilu.

4. Nyní v souboru pracovního postupu ve větvi: `.github/workflows/workflow.yml` nahraďte tajný klíč pro vstupní `publish-profile` akce nasazení webové aplikace Azure.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Po definování se zobrazí tajný klíč, jak je uvedeno níže.

    ![záleží](media/app-service-github-actions/app-service-secrets.png)

## <a name="setup-the-environment"></a>Nastavení prostředí

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

To závisí na jazyku a jazycích podporovaných službou Azure Web Apps. Tato část by měla být standardním postupem sestavení pro jednotlivé jazyky.

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
## <a name="deploy-the-web-app"></a>Nasazení webové aplikace

Chcete-li nasadit kód do webové aplikace, bude nutné použít akci `Azure/appservice-actions/webapp@master`. Tato akce má 4 parametry:

| **Ukazatele**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | Požadovanou Název webové aplikace Azure | 
| **publikování – profil** | Volitelné Publikování obsahu souboru profilu pomocí Nasazení webu tajných klíčů |
| **balíček** | Volitelné Cesta k balíčku nebo složce *. zip, *. War, *. jar nebo složka, která se má nasadit |
| **název slotu** | Volitelné Zadejte jinou existující patici, než je produkční slot. |

### <a name="deploy-using-publish-profile"></a>Nasadit pomocí profilu publikování

Níže je ukázkový pracovní postup pro sestavení a nasazení webové aplikace v Node. js do Azure pomocí profilu publikování.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Nasazení s použitím instančního objektu Azure

Níže je ukázkový pracovní postup pro sestavení a nasazení webové aplikace v Node. js do Azure pomocí instančního objektu Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Další kroky

Můžete najít naši sadu akcí seskupených do různých úložišť na GitHubu. Každá z nich obsahuje dokumentaci a příklady, které vám pomůžou používat GitHub pro CI/CD a nasazovat aplikace do Azure.

- [Přihlášení Azure](https://github.com/Azure/actions)

- [WebApp Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp pro kontejnery](https://github.com/Azure/webapps-container-deploy)

- [Přihlášení nebo odhlášení Docker](https://github.com/Azure/docker-login)

- [Události, které spouštějí pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Nasazení K8s](https://github.com/Azure/k8s-deploy)

- [Úvodní pracovní postupy](https://github.com/actions/starter-workflows)
