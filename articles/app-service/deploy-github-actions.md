---
title: Konfigurace CI/CD s akcemi GitHubu
description: Naučte se, jak nasadit kód pro Azure App Service z kanálu CI/CD s akcemi GitHubu. Přizpůsobte úkoly sestavení a proveďte složitá nasazení.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 54e4ce409eb9f2a6bedd7861b3e268311f886b49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273241"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Nasazení do App Service pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. Díky akcím Azure App Service pro GitHub můžete automatizovat svůj pracovní postup nasazení do [Azure App Service](overview.md) pomocí akcí GitHubu.

> [!IMPORTANT]
> Akce GitHubu jsou momentálně ve verzi beta. [Abyste se mohli připojit ke službě Preview](https://github.com/features/actions) pomocí svého účtu GitHubu, musíte se nejdřív zaregistrovat.
> 

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

## <a name="use-the-deployment-center"></a>Použití centra nasazení

Pomocí centra nasazení App Service můžete rychle začít s akcemi GitHubu. Tím se automaticky vygeneruje soubor pracovního postupu na základě vašeho zásobníku aplikace a potvrdí se do úložiště GitHub ve správném adresáři.

1. Přejděte na WebApp na webu Azure Portal.
1. Na levé straně klikněte na **centrum nasazení** .
1. V části **průběžné nasazování (CI/CD)** vyberte **GitHub** .
1. Pak vyberte **Akce GitHubu** .
1. Výběr úložiště GitHubu, větve a zásobníku aplikací pomocí rozevíracích seznamů
    - Pokud je vybraná větev chráněná, můžete i nadále přidávat soubor pracovního postupu. Než budete pokračovat, nezapomeňte zkontrolovat ochranu větví.
1. Na poslední obrazovce si můžete prohlédnout své volby a zobrazit náhled souboru pracovního postupu, který se potvrdí do úložiště. Pokud jsou výběry správné, klikněte na **Dokončit** .

Tím se soubor pracovního postupu potvrdí do úložiště. Spustí se okamžitě pracovní postup sestavení a nasazení vaší aplikace.

## <a name="add-the-workflow-manually"></a>Přidat pracovní postup ručně

V případě pracovního postupu Azure App Service má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Sestavení** | 1. Nastavte prostředí. <br /> 2. Sestavte webovou aplikaci. |
|**Nasazení** | 1. Nasaďte webovou aplikaci. |

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

# <a name="user-level-credentials"></a>[Přihlašovací údaje na úrovni uživatele](#tab/userlevel)

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

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

# <a name="app-level-credentials"></a>[Přihlašovací údaje na úrovni aplikace](#tab/applevel)

Přihlašovací údaje na úrovni aplikace můžete použít pomocí profilu publikování aplikace. Na portálu přejdete na stránku správy vaší aplikace. Na stránce **Přehled** klikněte na možnost **získat profil publikování** .

Obsah souboru budete potřebovat později.

---

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

# <a name="user-level-credentials"></a>[Přihlašovací údaje na úrovni uživatele](#tab/userlevel)

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

Pokud chcete použít [přihlašovací údaje na úrovni uživatele](#generate-deployment-credentials), vložte celý výstup JSON z příkazu Azure CLI do pole hodnota tajného klíče. Zadejte tajný kód jako název `AZURE_CREDENTIALS` .

Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `creds` Akce přihlášení do Azure. Příklad:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Přihlašovací údaje na úrovni aplikace](#tab/applevel)

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

Pokud chcete použít [přihlašovací údaje na úrovni aplikace](#generate-deployment-credentials), vložte obsah staženého souboru publikačního profilu do pole hodnota tajného klíče. Zadejte tajný kód jako název `azureWebAppPublishProfile` .

Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `publish-profile` akce nasazení webové aplikace Azure. Příklad:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

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

# <a name="user-level-credentials"></a>[Přihlašovací údaje na úrovni uživatele](#tab/userlevel)

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node.js do Azure pomocí instančního objektu Azure. Všimněte si `creds` , jak vstup odkazuje na `AZURE_CREDENTIALS` tajný kód, který jste vytvořili dříve.

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

# <a name="app-level-credentials"></a>[Přihlašovací údaje na úrovni aplikace](#tab/applevel)

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node.js do Azure pomocí profilu publikování aplikace. Všimněte si `publish-profile` , jak vstup odkazuje na `azureWebAppPublishProfile` tajný kód, který jste vytvořili dříve.

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