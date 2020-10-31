---
title: Vlastní CI kontejnerů/CD z akcí GitHubu
description: Naučte se používat akce GitHubu k nasazení vlastního kontejneru Linux do App Service z kanálu CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 068fc9dcb9a4f4a62c2dd879bf8144097452f1e0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099024"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Nasazení vlastního kontejneru pro App Service pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) nabízí flexibilitu při vytváření automatizovaného pracovního postupu vývoje softwaru. Pomocí [akce nasazení webu Azure](https://github.com/Azure/webapps-deploy)můžete automatizovat pracovní postup a nasadit vlastní kontejnery, které se [App Service](overview.md) pomocí akcí GitHubu.

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které jsou v pracovním postupu.

Pro pracovní postup kontejneru Azure App Service má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. načtěte instanční objekt nebo publikační profil. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Sestavení** | 1. Vytvořte prostředí. <br /> 2. Sestavte image kontejneru. |
|**Nasazení** | 1. Nasaďte image kontejneru. |

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořit účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).  
- Pracovní kontejner registru a aplikace Azure App Service pro kontejnery. Tento příklad používá Azure Container Registry. 
    - [Naučte se vytvořit kontejnerovou aplikaci Node.js pomocí Docker, nahrajte image kontejneru do registru a pak nasaďte image do Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

Doporučený způsob ověřování pomocí Azure App Services pro akce GitHubu je profil publikování. Můžete se také ověřit pomocí instančního objektu, ale proces vyžaduje více kroků. 

Uložte přihlašovací údaje k publikačnímu profilu nebo instančnímu objektu jako [tajný kód GitHubu](https://docs.github.com/en/actions/reference/encrypted-secrets) pro ověření v Azure. V rámci pracovního postupu budete mít přístup ke tajnému kódu. 

# <a name="publish-profile"></a>[Publikovat profil](#tab/publish-profile)

Profil publikování je přihlašovací údaje na úrovni aplikace. Nastavte svůj publikační profil jako tajný klíč GitHubu. 

1. V Azure Portal přejdete do služby App Service. 

1. Na stránce **Přehled** vyberte **získat profil publikování** .

    > [!NOTE]
    > Od října 2020 budou webové aplikace pro Linux potřebovat nastavení aplikace `WEBSITE_WEBDEPLOY_USE_SCM` nastavené na hodnotu `true` **před stažením souboru** . Tento požadavek se v budoucnu odebere.

1. Stažený soubor uložte. Pomocí obsahu souboru vytvoříte tajný klíč GitHubu.

# <a name="service-principal"></a>[Instanční objekt](#tab/service-principal)

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

V tomto příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného, názvem skupiny prostředků a názvem aplikace. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší aplikaci App Service. Zkopírujte tento objekt JSON pro pozdější verzi.

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

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód** .

Vložte obsah výstupu JSON jako hodnotu tajné proměnné. Zadejte tajný kód jako název `AZURE_CREDENTIALS` .

Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `creds` Akce přihlášení do Azure. Například:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>Konfigurace tajného kódu GitHubu pro ověřování

# <a name="publish-profile"></a>[Publikovat profil](#tab/publish-profile)

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód** .

Pokud chcete použít [přihlašovací údaje na úrovni aplikace](#generate-deployment-credentials), vložte obsah staženého souboru publikačního profilu do pole hodnota tajného klíče. Pojmenujte tajný klíč `AZURE_WEBAPP_PUBLISH_PROFILE` .

Když nakonfigurujete pracovní postup GitHubu, použijte `AZURE_WEBAPP_PUBLISH_PROFILE` v akci nasazení webové aplikace Azure. Například:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Instanční objekt](#tab/service-principal)

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód** .

Pokud chcete použít [přihlašovací údaje na úrovni uživatele](#generate-deployment-credentials), vložte celý výstup JSON z příkazu Azure CLI do pole hodnota tajného klíče. Zadejte tajný kód jako název `AZURE_CREDENTIALS` .

Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `creds` Akce přihlášení do Azure. Například:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Konfigurace tajných kódů GitHubu pro váš registr

Definování tajných kódů pro použití s akcí přihlášení k Docker 

1. V Azure Portal nebo Docker klikněte na kontejner a zkopírujte uživatelské jméno a heslo. 

2. Zadejte nový tajný klíč pro uživatelské jméno registru s názvem `REGISTRY_USERNAME` . 

3. Zadejte nový tajný klíč pro heslo registru s názvem `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Sestavení image kontejneru

Následující příklad ukazuje část pracovního postupu, která vytváří Node.JS image Docker. K přihlášení do soukromého registru kontejneru použijte [přihlašovací jméno Docker](https://github.com/azure/docker-login) . V tomto příkladu se používá Azure Container Registry, ale stejná akce funguje i pro jiné Registry. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Můžete také použít [přihlášení Docker](https://github.com/azure/docker-login) pro přihlášení k více registrům kontejnerů současně. Tento příklad obsahuje dva nové tajné kódy GitHubu pro ověřování pomocí docker.io.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Nasazení do kontejneru App Service

K nasazení image do vlastního kontejneru v App Service použijte `azure/webapps-deploy@v2` akci. Tato akce má sedm parametrů:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | Požadovanou Název aplikace App Service | 
| **publikování – profil** | Volitelné Platí pro Web Apps (Windows a Linux) a kontejnery webové aplikace (Linux). Scénář vícenásobného kontejneru není podporován. Publikovat \* obsah souboru profilu (. publishsettings) s nasazení webu tajnými klíči | 
| **název slotu** | Volitelné Zadejte jinou existující patici, než je produkční slot. |
| **balíček** | Volitelné Platí jenom pro webovou aplikaci: cesta k balíčku nebo složce. \*. zip, \* . War, \* . jar nebo složka, která se má nasadit |
| **fotografií** | Požadovanou Platí jenom pro kontejnery webové aplikace: zadejte plně kvalifikované názvy imagí kontejneru. Například ' myregistry.azurecr.io/nginx:latest ' nebo ' Python: 3.7.2-Alpine/'. Pro aplikaci s více kontejnery je možné zadat více názvů imagí kontejneru (oddělené víceřádkově). |
| **konfigurační soubor** | Volitelné Platí jenom pro kontejnery webové aplikace: cestu k souboru Docker-Compose. Musí být úplná cesta nebo relativní vzhledem k výchozímu pracovnímu adresáři. Vyžaduje se pro aplikace s více kontejnery. |
| **spuštění – příkaz** | Volitelné Zadejte spouštěcí příkaz. Pro ex. spuštění dotnet nebo dotnet filename.dll |

# <a name="publish-profile"></a>[Publikovat profil](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Instanční objekt](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Další kroky

Můžete najít naši sadu akcí seskupených do různých úložišť na GitHubu. Každá z nich obsahuje dokumentaci a příklady, které vám pomůžou používat GitHub pro CI/CD a nasazovat aplikace do Azure.

- [Akce pracovních postupů k nasazení do Azure](https://github.com/Azure/actions-workflow-samples)

- [Přihlášení k Azure](https://github.com/Azure/login)

- [WebApp Azure](https://github.com/Azure/webapps-deploy)

- [Přihlášení nebo odhlášení Docker](https://github.com/Azure/docker-login)

- [Události, které aktivují pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Nasazení K8s](https://github.com/Azure/k8s-deploy)

- [Úvodní pracovní postupy](https://github.com/actions/starter-workflows)
