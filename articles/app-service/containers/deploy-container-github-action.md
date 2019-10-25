---
title: Nasazení kontejneru z kanálu CI/CD s akcemi na GitHubu – Azure App Service | Microsoft Docs
description: Naučte se používat akce GitHubu k nasazení kontejneru do App Service
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
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809809"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>Akce GitHubu pro nasazení do Web App for Containers

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. Díky akcím Azure App Service pro GitHub můžete automatizovat pracovní postup nasazení [Azure Web Apps for Containers](https://azure.microsoft.com/services/app-service/containers/) pomocí akcí GitHubu.

> [!IMPORTANT]
> Akce GitHubu jsou momentálně ve verzi beta. [Abyste se mohli připojit ke službě Preview](https://github.com/features/actions) pomocí svého účtu GitHubu, musíte se nejdřív zaregistrovat.
> 

Pracovní postup je definovaný souborem YAML (. yml) v cestě `/.github/workflows/` v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup kontejneru webové aplikace Azure má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Ověřování** | 1. definování instančního objektu <br /> 2. vytvoření tajného kódu GitHubu |
|**Budování** | 1. nastavení prostředí <br /> 2. sestavení image kontejneru |
|**Nasazení** | 1. nasazení image kontejneru |

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

Následující příklad používá přihlašovací údaje na úrovni uživatele, tj. instanční objekt Azure pro nasazení. Použijte postup konfigurace tajného klíče:

1. V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných klíčů > Přidat nový tajný kód** .

2. Vložte obsah níže uvedeného `az cli` příkazu jako hodnotu tajné proměnné. Například, `AZURE_CREDENTIALS`.

    
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
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>Nasadit do kontejneru webové aplikace

Pokud chcete nasadit image do kontejneru webové aplikace, budete muset použít akci `Azure/appservice-actions/webapp@master`. Tato akce má 5 parametrů:

| **Ukazatele**  | **Vysvětlení**  |
|---------|---------|
| **název aplikace** | Požadovanou Název webové aplikace Azure | 
| **název slotu** | Volitelné Zadejte jinou existující patici, než je produkční slot. |
| **fotografií** | Požadovanou Zadejte plně kvalifikované názvy imagí kontejneru. Například ' myregistry.azurecr.io/nginx:latest ' nebo ' Python: 3.7.2-Alpine/'. Pro scénář s více kontejnery lze zadat více názvů imagí kontejneru (oddělené více řádky). |
| **konfigurační soubor** | Volitelné Cesta k souboru Docker-skládání Musí být úplná cesta nebo relativní vzhledem k výchozímu pracovnímu adresáři. Požadováno pro scénář s více kontejnery |
| **kontejner – příkaz** | Volitelné Zadejte příkaz pro spuštění. Pro ex. příkaz dotnet Run nebo dotnet filename. dll |

Níže je ukázkový pracovní postup pro sestavení a nasazení webové aplikace v Node. js do kontejneru webové aplikace Azure.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

- [Přihlášení Azure](https://github.com/Azure/actions)

- [WebApp Azure](https://github.com/Azure/webapps-deploy)

- [Azure WebApp pro kontejnery](https://github.com/Azure/webapps-container-deploy)

- [Přihlášení nebo odhlášení Docker](https://github.com/Azure/docker-login)

- [Události, které spouštějí pracovní postupy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [Nasazení K8s](https://github.com/Azure/k8s-deploy)

- [Úvodní pracovní postupy](https://github.com/actions/starter-workflows)
