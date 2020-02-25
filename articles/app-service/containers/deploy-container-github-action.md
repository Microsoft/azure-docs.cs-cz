---
title: Vlastní CI kontejnerů/CD z akcí GitHubu
description: Naučte se používat akce GitHubu k nasazení vlastního kontejneru Linux do App Service z kanálu CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: f32ea2ae0be66259ff153c24bfd10e179fddbbe5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559024"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Nasazení vlastního kontejneru pro App Service pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. Díky [akci Azure App Service pro kontejnery](https://github.com/Azure/webapps-container-deploy)můžete automatizovat pracovní postup nasazení aplikací jako [vlastních kontejnerů, které se App Service](https://azure.microsoft.com/services/app-service/containers/) pomocí akcí GitHubu.

> [!IMPORTANT]
> Akce GitHubu jsou momentálně ve verzi beta. [Abyste se mohli připojit ke službě Preview](https://github.com/features/actions) pomocí svého účtu GitHubu, musíte se nejdřív zaregistrovat.
> 

Pracovní postup je definovaný souborem YAML (. yml) v cestě `/.github/workflows/` ve vašem úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup kontejneru Azure App Service má soubor tři části:

|Část  |Úlohy  |
|---------|---------|
|**Ověřování** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Budování** | 1. Nastavte prostředí. <br /> 2. Sestavte image kontejneru. |
|**Nasazení** | 1. Nasaďte image kontejneru. |

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší App Service aplikaci, která je podobná níže. Zkopírujte tento objekt JSON pro ověřování z GitHubu.

 ```azurecli 
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

Následující příklad používá přihlašovací údaje na úrovni uživatele, tj. instanční objekt Azure pro nasazení. Použijte postup konfigurace tajného klíče:

1. V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných klíčů > Přidat nový tajný kód** .

2. Vložte obsah níže uvedeného `az cli` příkazu jako hodnotu tajné proměnné. například `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Nyní v souboru pracovního postupu ve větvi: `.github/workflows/workflow.yml` nahraďte tajný klíč v akci přihlášení Azure pomocí tajného klíče.

4. Podobně definujte následující další tajné kódy pro přihlašovací údaje registru kontejneru a nastavte je v akci přihlášení k Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Po definování se zobrazí tajné kódy, jak je uvedeno níže.

    ![tajné kódy kontejneru](../media/app-service-github-actions/app-service-secrets-container.png)

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

K nasazení image do vlastního kontejneru v App Service použijte akci `azure/webapps-container-deploy@v1`. Tato akce má pět parametrů:

| **Ukazatele**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | Požadovanou Název aplikace App Service | 
| **název slotu** | Volitelné Zadejte jinou existující patici, než je produkční slot. |
| **fotografií** | Požadovanou Zadejte plně kvalifikované názvy imagí kontejneru. Například ' myregistry.azurecr.io/nginx:latest ' nebo ' Python: 3.7.2-Alpine/'. Pro aplikaci s více kontejnery je možné zadat více názvů imagí kontejneru (oddělené víceřádkově). |
| **konfigurační soubor** | Volitelné Cesta k souboru Docker-skládání Musí být úplná cesta nebo relativní vzhledem k výchozímu pracovnímu adresáři. Vyžaduje se pro aplikace s více kontejnery. |
| **kontejner – příkaz** | Volitelné Zadejte spouštěcí příkaz. Pro ex. příkaz dotnet Run nebo dotnet filename. dll |

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node. js do vlastního kontejneru v App Service.

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
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Další kroky

Můžete najít naši sadu akcí seskupených do různých úložišť na GitHubu. Každá z nich obsahuje dokumentaci a příklady, které vám pomůžou používat GitHub pro CI/CD a nasazovat aplikace do Azure.

- [Přihlášení Azure](https://github.com/Azure/login)

- [WebApp Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp pro kontejnery](https://github.com/Azure/webapps-container-deploy)

- [Přihlášení nebo odhlášení Docker](https://github.com/Azure/docker-login)

- [Události, které spouštějí pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Nasazení K8s](https://github.com/Azure/k8s-deploy)

- [Pracovní postupy počáteční CI](https://github.com/actions/starter-workflows)

- [Úvodní pracovní postupy pro nasazení do Azure](https://github.com/Azure/actions-workflow-samples)
