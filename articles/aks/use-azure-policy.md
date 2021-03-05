---
title: Použití Azure Policy k zabezpečení clusteru
description: Použijte Azure Policy k zabezpečení clusteru AKS (Azure Kubernetes Service).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193408"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Zabezpečte svůj cluster pomocí Azure Policy

Pro zlepšení zabezpečení clusteru Azure Kubernetes Service (AKS) můžete použít a vynutilit integrované zásady zabezpečení v clusteru pomocí Azure Policy. [Azure Policy][azure-policy] pomáhá vyhovět standardům organizace a hodnotit dodržování předpisů v rozsahu. Po instalaci [doplňku Azure Policy pro AKS][kubernetes-policy-reference]můžete na svůj cluster použít jednotlivé definice zásad nebo skupiny definic zásad s názvem iniciativy (někdy označované jako policysets). Úplný seznam definic zásad AKS a iniciativ najdete v tématu [Azure Policy integrovaných definic pro AKS][aks-policies] .

V tomto článku se dozvíte, jak ve svém clusteru použít definice zásad a ověřit, jestli se tato přiřazení uplatňují.

## <a name="prerequisites"></a>Požadavky

- Existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].
- Doplněk Azure Policy pro AKS nainstalovaný v clusteru AKS. Pomocí těchto [kroků nainstalujte doplněk Azure Policy][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Přiřazení předdefinované definice zásady nebo iniciativy

Chcete-li použít definici nebo iniciativu zásady, použijte Azure Portal.

1. Přejděte ke službě Azure Policy v Azure Portal.
1. V levém podokně stránky Azure Policy vyberte **definice**.
1. V části **kategorie** vyberte `Kubernetes` .
1. Vyberte definici nebo iniciativu zásady, kterou chcete použít. V tomto příkladu vyberte `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniciativu.
1. Vyberte **Přiřadit**.
1. Nastavte **obor** na skupinu prostředků clusteru AKS s povoleným doplňkem Azure Policy.
1. Vyberte stránku **parametry** a aktualizujte **efekt** z `audit` na na `deny` , chcete-li zablokovat nová nasazení porušující základní iniciativu. Můžete také přidat další obory názvů pro vyloučení ze vyhodnocení. V tomto příkladu ponechte výchozí hodnoty.
1. Vyberte možnost **zkontrolovat + vytvořit** a **vytvořit** a odešlete přiřazení zásady.

## <a name="validate-a-azure-policy-is-running"></a>Ověřte, že je spuštěný Azure Policy.

Potvrďte přiřazení zásad k vašemu clusteru spuštěním následujících kroků:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Synchronizace zásad může trvat [až 20 minut, než se synchronizuje][azure-policy-assign-policy] do každého clusteru.

Výstup by měl vypadat přibližně takto:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Ověření odmítnutí privilegovaného pod

Pojďme nejdřív otestovat, co se stane, když naplánujete pod, pomocí kontextu zabezpečení `privileged: true` . Tento kontext zabezpečení přestupňování oprávnění pod. Iniciativa nepovoluje privilegované lusky, takže žádost bude odepřena v důsledku odmítnutí nasazení.

Vytvořte soubor s názvem `nginx-privileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Podle očekávání se nepovedlo naplánovat na, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

V poli se nedosáhnou fáze plánování, takže před přesunutím na neexistují žádné prostředky, které by bylo možné odstranit.

### <a name="test-creation-of-an-unprivileged-pod"></a>Vytvoření testu neprivilegovaného pod

V předchozím příkladu se image kontejneru automaticky pokusila použít kořen k navázání NGINX na port 80. Tuto žádost zamítla iniciativa zásad, takže se na začátku nespustí. Zkusíme teď spustit stejný NGINX pod bez privilegovaného přístupu.

Vytvořte soubor s názvem `nginx-unprivileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Vytvořte pod pomocí příkazu [kubectl Applu][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

V části se úspěšně naplánovalo. Když zkontrolujete stav pod, pomocí příkazu [kubectl Get lusky][kubectl-get] je *spuštěný*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Tento příklad ukazuje základní iniciativu ovlivňující jenom nasazení, která porušují zásady v kolekci. Povolená nasazení budou nadále fungovat.

Pomocí příkazu [kubectl Delete][kubectl-delete] odstraňte Nginx s neprivilegovaným příkazem a zadejte název vašeho manifestu YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Zakázat zásadu nebo iniciativu

Odebrání základní iniciativy:

1. Přejděte do podokna zásady na Azure Portal.
1. V levém podokně vyberte **přiřazení** .
1. Klikněte na tlačítko **...** vedle `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniciativy.
1. Vyberte **Odstranit přiřazení**.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak Azure Policy funguje:

- [Přehled služby Azure Policy][azure-policy]
- [Azure Policy iniciativ a zásad pro AKS][aks-policies]
- Odeberte [doplněk Azure Policy][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
