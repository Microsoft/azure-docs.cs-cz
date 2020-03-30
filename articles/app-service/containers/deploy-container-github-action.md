---
title: Vlastní kontejner CI/CD z akce GitHub
description: Zjistěte, jak pomocí akcí GitHubu nasadit vlastní kontejner Linuxu do služby App Service z kanálu CI/CD.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246972"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Nasazení vlastního kontejneru do služby App Service pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám poskytují flexibilitu při vytváření automatizovaného pracovního cyklu životního cyklu vývoje softwaru. Pomocí [akce služby Azure App Service pro kontejnery](https://github.com/Azure/webapps-container-deploy)můžete automatizovat pracovní postup a nasazovat aplikace jako [vlastní kontejnery do služby App Service](https://azure.microsoft.com/services/app-service/containers/) pomocí akcí GitHub.

> [!IMPORTANT]
> Akce GitHubu jsou v současné době v beta verzi. Musíte [se nejprve zaregistrovat a připojit se k náhledu](https://github.com/features/actions) pomocí svého účtu GitHub.
> 

Pracovní postup je definován souborem YAML (.yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup kontejneru služby Azure App Service má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Ověřování** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Sestavení** | 1. Nastavte prostředí. <br /> 2. Vytvořte image kontejneru. |
|**Nasadit** | 1. Nasaďte image kontejneru. |

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) můžete vytvořit pomocí příkazu [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v [příkazovém příkazu Azure CLI](https://docs.microsoft.com/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) na webu Azure Portal nebo výběrem tlačítka Try **It.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Výstup je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k aplikaci App Service podobné níže. Zkopírujte tento objekt JSON k ověření z GitHubu.

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
> Je vždy dobrým zvykem udělit minimální přístup. Můžete omezit obor ve výše uvedeném příkazu Az CLI na konkrétní aplikaci služby App Service a registru kontejnerů Azure, kam se vysouvají iimage kontejneru.

## <a name="configure-the-github-secret"></a>Konfigurace tajného klíče GitHubu

Níže uvedený příklad používá přihlašovací údaje na úrovni uživatele, tj. Postupujte podle pokynů pro konfiguraci tajného klíče:

1. V [GitHubu](https://github.com/), procházejte úložiště, vyberte **Nastavení > Tajemství > Přidat nový tajný klíč**

2. Vložte obsah níže `az cli` uvedeného příkazu jako hodnotu proměnné tajného klíče. Například, `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Nyní v souboru pracovního `.github/workflows/workflow.yml` postupu ve vaší větvi: nahraďte tajný klíč v aplikaci Azure přihlášení s tajným klíčem.

4. Podobně definujte následující další tajné kódy pro pověření registru kontejneru a nastavte je v akci přihlášení Dockeru. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Vidíte tajemství, jak je uvedeno níže, jakmile je definováno.

    ![tajné klíče kontejneru](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Sestavení image kontejneru

Následující příklad ukazuje část pracovního postupu, která vytváří image dockeru.

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

## <a name="deploy-to-an-app-service-container"></a>Nasazení do kontejneru služby App Service

Pokud chcete nasadit image do vlastního kontejneru ve službě App Service, použijte `azure/webapps-container-deploy@v1` akci. Tato akce má pět parametrů:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | (Povinné) Název aplikace App Service | 
| **název slotu** | (Nepovinné) Zadejte existující slot jiný než produkční slot |
| **Obrázky** | (Povinné) Zadejte plně kvalifikovaný název image kontejneru. Například 'myregistry.azurecr.io/nginx:latest' nebo 'python:3.7.2-alpine/'. Pro aplikaci s více kontejnery lze poskytnout více názvů bitových obrázků kontejneru (víceřádkové oddělené) |
| **konfigurační soubor** | (Nepovinné) Cesta souboru Docker-Compose. By měla být plně kvalifikovaná cesta nebo vzhledem k výchozímu pracovnímu adresáři. Vyžadováno pro aplikace s více kontejnery. |
| **kontejner-příkaz** | (Nepovinné) Zadejte příkaz start-up. Pro ex. dotnet run nebo dotnet filename.dll |

Níže je ukázkový pracovní postup pro sestavení a nasazení aplikace Node.js do vlastního kontejneru ve službě App Service.

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

Můžete najít naši sadu akcí seskupených do různých úložišť na GitHubu, z nichž každá obsahuje dokumentaci a příklady, které vám pomohou používat GitHub pro CI/CD a nasadit aplikace do Azure.

- [Přihlášení k Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp pro kontejnery](https://github.com/Azure/webapps-container-deploy)

- [Přihlášení/odhlášení dockeru](https://github.com/Azure/docker-login)

- [Události, které aktivují pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s nasadit](https://github.com/Azure/k8s-deploy)

- [Počáteční pracovní postupy CI](https://github.com/actions/starter-workflows)

- [Počáteční pracovní postupy pro nasazení do Azure](https://github.com/Azure/actions-workflow-samples)
