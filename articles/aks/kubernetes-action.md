---
title: Vytváření, testování a nasazování kontejnerů do služby Azure Kubernetes pomocí akcí GitHub
description: Přečtěte si, jak pomocí akcí GitHubu nasadit kontejner do Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595361"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Akce GitHubu pro nasazení do služby Kubernetes

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám poskytují flexibilitu při vytváření automatizovaného pracovního cyklu životního cyklu vývoje softwaru. Akce [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) Kubernetes usnadňuje nasazení do clusterů služby Azure Kubernetes. Akce nastaví cílový kontext clusteru AKS, který může být použit jinými akcemi, jako [je azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master), [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) atd.

Pracovní postup je definován souborem YAML (.yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup cílení AKS má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Ověřování** | Přihlášení do registru soukromého kontejneru (ACR) |
|**Sestavení** | Vytvoření & stlačení montovky kontejneru  |
|**Nasadit** | 1. Nastavení cílového clusteru AKS |
| |2. Vytvoření obecného tajného klíče/tajného klíče registru dockeru v clusteru Kubernetes  |
||3. Nasazení do clusteru Kubernetes|

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) můžete vytvořit pomocí příkazu [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v [příkazovém příkazu Azure CLI](https://docs.microsoft.com/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) na webu Azure Portal nebo výběrem tlačítka Try **It.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Ve výše uvedeném příkazu nahraďte zástupné symboly ID předplatného a skupinou prostředků. Výstup emituje pověření přiřazení role, které poskytují přístup k prostředku. Příkaz by měl výstup JSON objekt podobný tomuto.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Zkopírujte tento objekt JSON, který můžete použít k ověření z GitHubu.

## <a name="configure-the-github-secrets"></a>Konfigurace tajných klíčů GitHubu

Postupujte podle pokynů pro konfiguraci tajných kódů:

1. V [GitHubu](https://github.com/)přejděte do svého repozitáře a vyberte **Nastavení > Tajemství > Přidat nový tajný klíč**.

    ![Tajemství](media/kubernetes-action/secrets.png)

2. Vložte obsah výše `az cli` uvedeného příkazu jako hodnotu proměnné tajného klíče. Například, `AZURE_CREDENTIALS`.

3. Podobně definujte následující další tajné kódy pro pověření registru kontejneru a nastavte je v akci přihlášení Dockeru. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Uvidíte tajemství, jak je uvedeno níže, jakmile je definováno.

    ![kubernetes-tajemství](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Vytvoření image kontejneru a nasazení do clusteru služby Azure Kubernetes

Sestavení a nabízení bitové kopie `Azure/docker-login@v1` kontejneru se provádí pomocí akce. Chcete-li nasadit image kontejneru do AKS, budete muset použít `Azure/k8s-deploy@v1` akci. Tato akce má pět parametrů:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **namespace** | (Nepovinné) Zvolte cílový obor názvů Kubernetes. Pokud není k dispozici obor názvů, budou příkazy spuštěny ve výchozím oboru názvů. | 
| **Manifesty** |  (Povinné) Cesta k souborům manifestu, které budou použity pro nasazení |
| **Obrázky** | (Nepovinné) Plně kvalifikovaná adresa URL zdroje obrázku (obrázků), která má být použita pro substituce v souborech manifestu |
| **imagepullsecrets** | (Nepovinné) Název tajného klíče registru dockeru, který již byl v clusteru nastaven. Každý z těchto tajných názvů je přidán do pole imagePullSecrets pro úlohy nalezené ve vstupních souborech manifestu |
| **kubectl-verze** | (Nepovinné) Nainstaluje konkrétní verzi kubectl binární |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Nasazení do clusteru služby Azure Kubernetes

Komplexní pracovní postup pro vytváření ibi kontejnerů a nasazování do clusteru služby Azure Kubernetes.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Další kroky

Naši sadu akcí najdete v různých repozitářích na GitHubu, z nichž každá obsahuje dokumentaci a příklady, které vám pomůžou používat GitHub pro CI/CD a nasadit aplikace do Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-kontext](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-vytvořit-tajný klíč](https://github.com/Azure/k8s-create-secret)

- [k8s-nasazení](https://github.com/Azure/k8s-deploy)

- [nasazení webových aplikací-kontejnerů](https://github.com/Azure/webapps-container-deploy)

- [akce-workflow-ukázky](https://github.com/Azure/actions-workflow-samples)
