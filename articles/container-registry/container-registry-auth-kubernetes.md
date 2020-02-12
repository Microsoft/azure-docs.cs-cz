---
title: Ověření z clusteru Kubernetes
description: Naučte se, jak poskytnout cluster Kubernetes s přístupem k imagím ve službě Azure Container Registry vytvořením tajného kódu pro vyžádání obsahu pomocí instančního objektu.
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154891"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Vyžádání imagí z Azure Container Registry do clusteru Kubernetes

Můžete použít službu Azure Container Registry jako zdroj imagí kontejneru s jakýmkoli clusterem Kubernetes, včetně "místních" clusterů Kubernetes, jako jsou [minikube](https://minikube.sigs.k8s.io/) a [druh](https://kind.sigs.k8s.io/). V tomto článku se dozvíte, jak vytvořit Kubernetes tajný klíč pro vyžádání obsahu, který je založený na Azure Active Directory instančního objektu. Pak použijte tajný klíč k vyžádání imagí z služby Azure Container Registry v nasazení Kubernetes.

> [!TIP]
> Pokud používáte spravovanou [službu Azure Kubernetes](../aks/intro-kubernetes.md), můžete [cluster integrovat](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) do cílového registru kontejneru Azure pro stažení imagí. 

V tomto článku se předpokládá, že jste už vytvořili privátní službu Azure Container Registry. Také musíte mít cluster s Kubernetes spuštěný a přístupný prostřednictvím nástroje příkazového řádku `kubectl`.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Pokud heslo instančního objektu neuložíte nebo si ho nezapamatujete, můžete ho resetovat pomocí příkazu [AZ AD SP Credential Reset][az-ad-sp-credential-reset] :

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Tento příkaz vrátí nové platné heslo k instančnímu objektu.

## <a name="create-an-image-pull-secret"></a>Vytvoření bitové kopie pro vyžádání obsahu

Kubernetes používá k ukládání informací potřebných k ověření vašeho registru *tajný klíč pro získání bitové kopie* . Pokud chcete vytvořit tajný kód pro vyžádání obsahu pro službu Azure Container Registry, zadejte ID instančního objektu, heslo a adresu URL registru. 

Vytvoření bitové kopie pro vyžádání obsahu pomocí následujícího příkazu `kubectl`:

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
kde:

| Hodnota | Popis |
| :--- | :--- |
| `secret-name` | Název tajného klíče pro čtení z image, například *ACR-Secret* |
| `namespace` | Kubernetes obor názvů pro vložení tajného kódu do <br/> Nutné pouze v případě, že chcete tajný klíč umístit v jiném oboru názvů, než je výchozí obor názvů |
| `container-registry-name` | Název vašeho registru kontejneru Azure |
| `service-principal-ID` | ID instančního objektu, který bude Kubernetes používat pro přístup k registru |
| `service-principal-password` | Heslo instančního objektu |

## <a name="use-the-image-pull-secret"></a>Použít tajný klíč pro vyžádání image

Po vytvoření bitové kopie pro vyžádání obsahu image můžete použít k vytváření Kubernetesch lusků a nasazení. Zadejte název tajného klíče v části `imagePullSecrets` v souboru nasazení. Například:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

V předchozím příkladu je `your-awesome-app:v1` název obrázku, který se má načíst ze služby Azure Container Registry, a `acr-secret` je název tajného kódu pro vyžádání obsahu, který jste vytvořili pro přístup k registru. Když nasadíte pod, Kubernetes automaticky načte image z registru, pokud ještě není v clusteru.


## <a name="next-steps"></a>Další kroky

* Další informace o práci s instančními objekty a Azure Container Registry najdete v tématu [ověřování Azure Container Registry pomocí instančních objektů](container-registry-auth-service-principal.md) .
* Další informace o tajných klíčích pro vyžádání imagí najdete v [dokumentaci k Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) .


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset