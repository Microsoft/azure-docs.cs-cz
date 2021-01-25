---
title: Sestavování, testování a nasazení kontejnerů do služby Azure Kubernetes pomocí akcí GitHubu
description: Naučte se používat akce GitHubu k nasazení vašeho kontejneru do Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 94134360de49a066f825cbb0c85712995d90b37f
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761451"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Akce GitHubu pro nasazení do služby Kubernetes

[Akce GitHubu](https://docs.github.com/en/actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. K nasazení do kontejnerů z Azure Container Registry do služby Azure Kubernetes pomocí akcí GitHubu můžete použít několik akcí Kubernetes. 

## <a name="prerequisites"></a>Požadavky 

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).  
- Funkční cluster Kubernetes
    - [Kurz: Příprava aplikace pro službu Azure Kubernetes](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Pro pracovní postup cílící na AKS má soubor tři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | Přihlášení k privátnímu registru kontejnerů (ACR) |
|**Sestavení** | Sestavení image kontejneru &m vložením  |
|**Nasazení** | 1. nastavení cílového clusteru AKS |
| |2. vytvoření tajného kódu registru Generic/Docker-Registry v clusteru Kubernetes  |
||3. nasazení do clusteru Kubernetes|

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Ve výše uvedeném příkazu nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného a skupinou prostředků. Výstupem jsou přihlašovací údaje přiřazení role, které poskytují přístup k vašemu prostředku. Příkaz by měl výstup objektu JSON podobný tomuto.

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

## <a name="configure-the-github-secrets"></a>Konfigurace tajných kódů GitHubu

Použijte postup konfigurace tajných kódů:

1. V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

    ![Snímek obrazovky se zobrazí odkaz Přidat nový tajný kód pro úložiště.](media/kubernetes-action/secrets.png)

2. Vložte obsah výše uvedeného `az cli` příkazu jako hodnotu tajné proměnné. Například `AZURE_CREDENTIALS`.

3. Podobně definujte následující další tajné kódy pro přihlašovací údaje registru kontejneru a nastavte je v akci přihlášení k Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Po definování se zobrazí tajné kódy, jak je uvedeno níže.

    ![Snímek obrazovky se zobrazí stávající tajná klíčová úložiště.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Sestavení image kontejneru a nasazení do clusteru služby Azure Kubernetes

Sestavení a vložení imagí kontejneru se provádí pomocí `Azure/docker-login@v1` akce. 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Nasazení do clusteru služby Azure Kubernetes

K nasazení image kontejneru do AKS budete muset použít `Azure/k8s-deploy@v1` akci. Tato akce má pět parametrů:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **hosting** | Volitelné Vyberte cílový obor názvů Kubernetes. Pokud obor názvů není zadaný, příkazy se spustí ve výchozím oboru názvů. | 
| **manifesty** |  Požadovanou Cesta k souborům manifestu, které se použijí pro nasazení |
| **fotografií** | Volitelné Plně kvalifikovaná adresa URL pro obrázky, které se mají použít k nahrazení souborů manifestu |
| **imagepullsecrets** | Volitelné Název tajného klíče registru Docker-Registry, který již byl nastaven v rámci clusteru. Každý z těchto tajných názvů se přidá do pole imagePullSecrets pro úlohy, které se nacházejí ve vstupních souborech manifestu. |
| **kubectl – verze** | Volitelné Nainstaluje specifickou verzi kubectl binárního souboru. |


Než budete moct nasadit do AKS, budete muset nastavit cílový obor názvů Kubernetes a vytvořit bitovou kopii pro vyžádání obsahu image. Další informace o tom, jak fungují image, najdete v tématu [vyžádání imagí z Azure Container Registry do clusteru Kubernetes](../container-registry/container-registry-auth-kubernetes.md). 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Dokončete nasazení s `k8s-deploy` akcí. Nahraďte proměnné prostředí hodnotami pro vaši aplikaci. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už cluster Kubernetes, registr kontejnerů a úložiště nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků a vašeho úložiště GitHub. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o službě Azure Kubernetes](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Další akce GitHubu Kubernetes

* [Instalační program nástroje Kubectl](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): nainstaluje na spouštěč konkrétní verzi Kubectl.
* [Kontext Kubernetes sady](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): Nastavte cílový kontext clusteru Kubernetes, který budou používat jiné akce, nebo spusťte všechny příkazy kubectl.
* [Kontext AKS sady](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): Nastavte cílový kontext clusteru služby Azure Kubernetes.
* [Kubernetes vytvořit tajný klíč](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): v clusteru Kubernetes vytvořte obecný tajný klíč nebo tajný klíč registru Docker-Registry.
* [Kubernetes Deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): zanesli a nasaďte manifesty do clusterů Kubernetes.
* [Setup Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): Nainstalujte specifickou verzi Helm binárního souboru na spouštěče.
* [Kubernetes zanesli](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): soubor manifestu zanesli, který se má použít pro nasazení pomocí helm2, kustomize nebo kompose.
* [Kubernetes Lint](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): Ověřte/Lint soubory manifestu.
