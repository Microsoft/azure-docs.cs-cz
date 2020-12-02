---
title: Použití identit spravovaných pod Azure Active Directory ve službě Azure Kubernetes (Preview)
description: Naučte se, jak používat spravované identity pod AAD pod správou Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 150e2e71a4db8ab07caad479ae098d5b9eb746da
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466827"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Použití identit spravovaných pod Azure Active Directory ve službě Azure Kubernetes (Preview)

Azure Active Directory identity pod správou používají primitivy Kubernetes k přidružení [spravovaných identit k prostředkům][az-managed-identities] a identitám Azure v Azure Active Directory (AAD) s lusky. Správci vytvářejí identity a vazby jako Kubernetes primitiva, které umožňují luskům přístup k prostředkům Azure, které spoléhají na AAD jako na poskytovatele identity.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Než začnete

Musíte mít nainstalované následující prostředky:

* Rozhraní příkazového řádku Azure, verze 2.8.0 nebo novější
* `azure-preview`Verze rozšíření 0.4.68 nebo novější

### <a name="limitations"></a>Omezení

* Pro cluster je povoleno maximálně 50. identit.
* V clusteru je povoleno maximálně 50 pod výjimkami identity.
* Identity pod správou jsou dostupné jenom pro fondy uzlů Linux.

### <a name="register-the-enablepodidentitypreview"></a>Zaregistrujte `EnablePodIdentityPreview`

Zaregistrujte `EnablePodIdentityPreview` funkci:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Instalace rozhraní příkazového `aks-preview` řádku Azure

Budete také potřebovat rozšíření Azure CLI AKS ve verzi *Preview* 0.4.64 nebo novější. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] . Nebo nainstalujte jakékoli dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Vytvoření clusteru AKS se spravovanými identitami

Vytvořte cluster AKS se zapnutou spravovanou identitou a s povolenou identitou pod správou identity. Následující příkazy pomocí příkazu [AZ Group Create][az-group-create] vytvoří skupinu prostředků s názvem *myResourceGroup* a pomocí příkazu [AZ AKS Create][az-aks-create] vytvoříte cluster AKS s názvem *myAKSCluster* ve skupině prostředků *myResourceGroup* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity
```

Přihlaste se ke svému clusteru AKS pomocí [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz také stáhne a nakonfiguruje `kubectl` klientský certifikát na vašem vývojovém počítači.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>Vytvoření identity

Vytvořte identitu pomocí [AZ identity Create][az-identity-create] a nastavte *IDENTITY_CLIENT_ID* a *IDENTITY_RESOURCE_ID* proměnných.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="create-a-pod-identity"></a>Vytvoření identity pod

Vytvořte identitu pod pro cluster pomocí `az aks pod-identity add` .

> [!IMPORTANT]
> Aby bylo možné `Owner` vytvořit vazbu identity a role, musíte mít ve svém předplatném příslušná oprávnění.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Když v clusteru AKS povolíte pod správou identity, do oboru názvů *Kube-System* se přidá AzurePodIdentityException s názvem *AKS-addon-Exception* . AzurePodIdentityException umožňuje luskům s určitými štítky pro přístup ke koncovému bodu Azure Instance Metadata Service (IMDS) bez zachycení serverem NMI (Node-Managed identity). *Výjimka AKS-addon-* umožňuje použít doplňky AKS, jako je například identita typu AAD pod správou, aby fungovala bez nutnosti ruční konfigurace AzurePodIdentityException. Volitelně můžete přidat, odebrat a aktualizovat AzurePodIdentityException pomocí `az aks pod-identity exception add` , `az aks pod-identity exception delete` , `az aks pod-identity exception update` nebo `kubectl` .

## <a name="run-a-sample-application"></a>Spustit ukázkovou aplikaci

Aby objekt pod používal identitu pod správou AAD, musí mít popisek *aadpodidbinding* s hodnotou, která odpovídá selektoru z *AzureIdentityBinding*. Pokud chcete spustit ukázkovou aplikaci pomocí identity pod správou AAD, vytvořte `demo.yaml` soubor s následujícím obsahem. Hodnoty z předchozích kroků nahraďte *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* a *IDENTITY_RESOURCE_GROUP* . Nahraďte *SUBSCRIPTION_ID* číslem svého předplatného.

> [!NOTE]
> V předchozích krocích jste vytvořili proměnné *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* a *IDENTITY_RESOURCE_GROUP* . Můžete použít příkaz, například `echo` k zobrazení hodnoty, kterou jste nastavili pro proměnné, například `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Všimněte si, že definice pod má popisek *aadpodidbinding* s hodnotou, která odpovídá názvu identity pod, kterou jste spustili `az aks pod-identity add` v předchozím kroku.

Nasaďte `demo.yaml` do stejného oboru názvů jako identitu pod a pomocí `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Ověřte, že se ukázková aplikace úspěšně spustila pomocí `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Ověřte, že protokoly obsahují token, který byl úspěšně získán a operace *Get* je úspěšná.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Vyčištění

Pokud chcete z clusteru odebrat identitu ze spravovaného AAD pod, odeberte ukázkovou aplikaci a identitu pod v clusteru. Pak odeberte identitu.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných identitách najdete v tématu [spravované identity pro prostředky Azure][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
