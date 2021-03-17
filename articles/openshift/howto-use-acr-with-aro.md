---
title: Použití Azure Container Registry s využitím Azure Red Hat OpenShift
description: Naučte se, jak načíst a spustit kontejner z Azure Container Registry v clusteru Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634409"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Použití Azure Container Registry s Azure Red Hat OpenShift (ARO)

Azure Container Registry (ACR) je spravovaná služba registrů kontejnerů, kterou můžete použít k ukládání privátních imagí kontejnerů Docker s podnikovými funkcemi, jako je geografická replikace. Pokud chcete získat přístup k ACR z clusteru ARO, cluster se může ověřit pomocí ACR uložení přihlašovacích údajů Docker do tajného klíče Kubernetes.  Stejně tak cluster ARO může použít imagePullSecret ve specifikaci pod k ověření proti registru při načítání image. V tomto článku se dozvíte, jak nastavit Azure Container Registry pomocí clusteru Azure Red Hat OpenShift k ukládání a vybírání privátních imagí kontejnerů Docker.

## <a name="prerequisites"></a>Požadavky

V tomto průvodci se předpokládá, že máte existující Azure Container Registry. Pokud to neuděláte, pomocí pokynů Azure Portal nebo [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md) vytvořte registr kontejnerů.

Tento článek také předpokládá, že máte existující cluster Azure Red Hat OpenShift a máte nainstalované rozhraní příkazového `oc` řádku. V takovém případě postupujte podle pokynů v [kurzu Vytvoření clusteru](tutorial-create-cluster.md)společnosti.

## <a name="get-a-pull-secret"></a>Získání tajného kódu pro vyžádání obsahu

Pro přístup k registru z vašeho clusteru ARO budete potřebovat tajný klíč pro stažení z ACR.

Pokud chcete získat přihlašovací údaje pro přístup z více klíčů, můžete použít Azure Portal nebo Azure CLI.

Pokud používáte Azure Portal, přejděte k instanci služby ACR a vyberte **přístupové klíče**.  `docker-username`Je název vašeho registru kontejneru, pro něj použijte buď heslo, nebo password2 `docker-password` .

![Přístupové klíče](./media/acr-access-keys.png)

Místo toho můžete pomocí Azure CLI získat tyto přihlašovací údaje:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>Vytvoření tajného klíče Kubernetes

Nyní tyto přihlašovací údaje použijeme k vytvoření tajného klíče Kubernetes. Pomocí přihlašovacích údajů ACR spusťte následující příkaz:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Tento tajný klíč bude uložen v aktuálním projektu OpenShift (Kubernetes obor názvů) a bude odkazovat pouze na lusky vytvořené v tomto projektu.  Další pokyny k vytvoření tajného klíče pro vyžádání obsahu clusteru najdete v tomto [dokumentu](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) .

## <a name="create-a-pod-using-a-private-registry-image"></a>Vytvoření pod použitím privátní image registru

Teď, když jsme připojili váš cluster ARO k vašemu ACRu, pojďme z vaší ACRy získat obrázek a vytvořit pod.

Začněte s podSpec a zadejte tajný klíč, který jste vytvořili jako imagePullSecret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Pokud chcete otestovat, jestli je vaše zařízení pod zprovozněním, spusťte tento příkaz a počkejte, než se **spustí** stav:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Další kroky

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure CLI](../container-registry/container-registry-get-started-azure-cli.md)
