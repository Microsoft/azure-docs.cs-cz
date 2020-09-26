---
title: Vlastní CI kontejnerů/CD z akcí GitHubu
description: Naučte se používat akce GitHubu k nasazení vlastního kontejneru Linux do App Service z kanálu CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 7f2824f4dcacb26d8941f51db6129aea0bb5f915
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273275"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Nasazení vlastního kontejneru pro App Service pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. Díky [akci Azure App Service pro kontejnery](https://github.com/Azure/webapps-container-deploy)můžete automatizovat pracovní postup nasazení vlastních kontejnerů [App Service](overview.md) pomocí akcí GitHubu.

> [!IMPORTANT]
> Akce GitHubu jsou momentálně ve verzi beta. [Abyste se mohli připojit ke službě Preview](https://github.com/features/actions) pomocí svého účtu GitHubu, musíte se nejdřív zaregistrovat.
> 

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup kontejneru Azure App Service má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Sestavení** | 1. Nastavte prostředí. <br /> 2. Sestavte image kontejneru. |
|**Nasazení** | 1. Nasaďte image kontejneru. |

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

V předchozím příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného a názvem skupiny prostředků. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší App Service aplikaci, která je podobná níže. Zkopírujte tento objekt JSON pro pozdější verzi.

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
> Je vždy dobrým zvykem udělit minimální přístup. Můžete omezit rozsah výše uvedeného příkazu AZ CLI na konkrétní aplikaci App Service a Azure Container Registry, do které jsou obrázky kontejneru vloženy.

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

Vložte obsah výstupu JSON z části [vytvořit instanční objekt](#create-a-service-principal) jako hodnotu pro tajnou proměnnou. Zadejte tajný kód jako název `AZURE_CREDENTIALS` .

Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `creds` Akce přihlášení do Azure. Příklad:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

Podobně definujte následující další tajné kódy pro přihlašovací údaje registru kontejneru a nastavte je v akci přihlášení k Docker.

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>Sestavení image kontejneru

Následující příklad ukazuje část pracovního postupu, která vytváří image Docker.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Nasazení do kontejneru App Service

K nasazení image do vlastního kontejneru v App Service použijte `azure/webapps-container-deploy@v2` akci. Tato akce má pět parametrů:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | Požadovanou Název aplikace App Service | 
| **název slotu** | Volitelné Zadejte jinou existující patici, než je produkční slot. |
| **fotografií** | Požadovanou Zadejte plně kvalifikované názvy imagí kontejneru. Například ' myregistry.azurecr.io/nginx:latest ' nebo ' Python: 3.7.2-Alpine/'. Pro aplikaci s více kontejnery je možné zadat více názvů imagí kontejneru (oddělené víceřádkově). |
| **konfigurační soubor** | Volitelné Cesta k souboru Docker-skládání Musí být úplná cesta nebo relativní vzhledem k výchozímu pracovnímu adresáři. Vyžaduje se pro aplikace s více kontejnery. |
| **kontejner – příkaz** | Volitelné Zadejte spouštěcí příkaz. Pro ex. spuštění dotnet nebo dotnet filename.dll |

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node.js do vlastního kontejneru v App Service. Všimněte si `creds` , jak vstup odkazuje na `AZURE_CREDENTIALS` tajný kód, který jste vytvořili dříve.

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
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v2
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

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