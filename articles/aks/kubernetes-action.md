---
title: Sestavování, testování a nasazení kontejnerů do služby Azure Kubernetes pomocí akcí GitHubu
description: Naučte se používat akce GitHubu k nasazení vašeho kontejneru do Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 7743a3a8d6e77affd6229b648ab79b5b2f07a0af
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564096"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Akce GitHubu pro nasazení do služby Kubernetes

[Akce GitHubu](https://help.github.com/en/articles/about-github-actions) vám nabízí flexibilitu při vytváření automatizovaného pracovního postupu životního cyklu vývoje softwaru. Akce Kubernetes [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) usnadňuje nasazení do clusterů služby Azure Kubernetes. Akce nastaví cílový kontext clusteru AKS, který můžou použít jiné akce jako [Azure/k8s-Deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/k8s – Create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) atd., nebo spustit všechny příkazy kubectl.

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

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

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

2. Vložte obsah výše uvedeného `az cli` příkazu jako hodnotu tajné proměnné. Například, `AZURE_CREDENTIALS`.

3. Podobně definujte následující další tajné kódy pro přihlašovací údaje registru kontejneru a nastavte je v akci přihlášení k Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Po definování se zobrazí tajné kódy, jak je uvedeno níže.

    ![Snímek obrazovky se zobrazí stávající tajná klíčová úložiště.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Sestavení image kontejneru a nasazení do clusteru služby Azure Kubernetes

Sestavení a vložení imagí kontejneru se provádí pomocí `Azure/docker-login@v1` akce. K nasazení image kontejneru do AKS budete muset použít `Azure/k8s-deploy@v1` akci. Tato akce má pět parametrů:

| **Parametr**  | **Vysvětlení**  |
|---------|---------|
| **hosting** | Volitelné Vyberte cílový obor názvů Kubernetes. Pokud obor názvů není zadaný, příkazy se spustí ve výchozím oboru názvů. | 
| **manifesty** |  Požadovanou Cesta k souborům manifestu, které se použijí pro nasazení |
| **fotografií** | Volitelné Plně kvalifikovaná adresa URL pro obrázky, které se mají použít k nahrazení souborů manifestu |
| **imagepullsecrets** | Volitelné Název tajného klíče registru Docker-Registry, který již byl nastaven v rámci clusteru. Každý z těchto tajných názvů se přidá do pole imagePullSecrets pro úlohy, které se nacházejí ve vstupních souborech manifestu. |
| **kubectl – verze** | Volitelné Nainstaluje specifickou verzi kubectl binárního souboru. |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Nasazení do clusteru služby Azure Kubernetes

Koncový postup pro vytváření imagí kontejnerů a nasazování do clusteru služby Azure Kubernetes.

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

Naši sadu akcí můžete najít v různých úložištích na GitHubu. Každá z nich obsahuje dokumentaci a příklady, které vám pomůžou s používáním GitHubu pro CI/CD a nasazení aplikací do Azure.

- [nastavení – kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-Context](https://github.com/Azure/k8s-set-context)

- [AKS-set-Context](https://github.com/Azure/aks-set-context)

- [k8s-vytvoření-tajného klíče](https://github.com/Azure/k8s-create-secret)

- [k8s – nasazení](https://github.com/Azure/k8s-deploy)

- [webapps-kontejner – nasazení](https://github.com/Azure/webapps-container-deploy)

- [Actions-Workflow-Samples](https://github.com/Azure/actions-workflow-samples)
