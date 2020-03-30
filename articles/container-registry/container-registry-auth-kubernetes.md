---
title: Ověření z clusteru Kubernetes
description: Zjistěte, jak poskytnout clusteru Kubernetes přístup k ibi v registru kontejnerů Azure vytvořením tajného klíče pro vyžádat pomocí instančního objektu
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77154891"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Vyžádat ibi z registru kontejnerů Azure do clusteru Kubernetes

Registr kontejnerů Azure můžete použít jako zdroj ibi kontejnerů s libovolným clusterem Kubernetes, včetně "místních" klastrů Kubernetes, jako jsou [minikube](https://minikube.sigs.k8s.io/) a [kind](https://kind.sigs.k8s.io/). Tento článek ukazuje, jak vytvořit tajný klíč obnovení Kubernetes na základě instančního objektu služby Azure Active Directory. Potom použijte tajný klíč k vyžádat image z registru kontejnerů Azure v nasazení Kubernetes.

> [!TIP]
> Pokud používáte spravovanou [službu Azure Kubernetes Service](../aks/intro-kubernetes.md), můžete také [integrovat svůj cluster](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) s cílovým registrem kontejnerů Azure pro vyžádat image. 

Tento článek předpokládá, že jste už vytvořili soukromý registr kontejnerů Azure. Musíte mít také cluster Kubernetes spuštěný `kubectl` a přístupný prostřednictvím nástroje příkazového řádku.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Pokud heslo hlavního povinného servisu neuložíte nebo si nezapamatujete, můžete ho obnovit pomocí příkazu [resetování pověření az az ad sp:][az-ad-sp-credential-reset]

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Tento příkaz vrátí nové, platné heslo pro instanční objekt.

## <a name="create-an-image-pull-secret"></a>Vytvoření tajného klíče pro vytažení obrázku

Kubernetes používá *tajný klíč pro vyprošťuji obrázku* k ukládání informací potřebných k ověření do registru. Chcete-li vytvořit tajný klíč vyžádat pro registr kontejneru Azure, zadejte ID instančního objektu, heslo a adresu URL registru. 

Vytvořte tajný klíč pro `kubectl` vytažení obrázku pomocí následujícího příkazu:

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
| `secret-name` | Název obrázku vytáhnout tajemství, například *acr-tajné* |
| `namespace` | Kubernetes obor názvů, aby tajný klíč do <br/> Potřebujete pouze v případě, že chcete tajný klíč umístit do jiného oboru názvů, než je výchozí obor názvů. |
| `container-registry-name` | Název registru kontejnerů Azure |
| `service-principal-ID` | ID instančního objektu, který bude k přístupu k registru použit společností Kubernetes |
| `service-principal-password` | Heslo hlavního povinného servisu |

## <a name="use-the-image-pull-secret"></a>Použít tajný klíč pro vytažení obrázku

Jakmile vytvoříte tajný klíč pro vytažení obrázku, můžete ho použít k vytvoření kubernetes podů a nasazení. Zadejte název tajného `imagePullSecrets` klíče v části v souboru nasazení. Například:

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

V předchozím příkladu `your-awesome-app:v1` je název bitové kopie naváděné z registru kontejneru Azure a `acr-secret` je název tajného klíče pro vyžádat, který jste vytvořili pro přístup k registru. Při nasazení pod, Kubernetes automaticky vytáhne bitovou kopii z registru, pokud již není k dispozici v clusteru.


## <a name="next-steps"></a>Další kroky

* Další informace o práci s objekty zabezpečení služeb a Azure Container Registry najdete v tématu [ověřování registru kontejnerů Azure s objekty zabezpečení služeb](container-registry-auth-service-principal.md)
* Další informace o tajných informacích o vytažení obrázků v [dokumentaci Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset