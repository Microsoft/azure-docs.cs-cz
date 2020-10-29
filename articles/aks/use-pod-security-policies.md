---
title: Použití pod zásadami zabezpečení ve službě Azure Kubernetes Service (AKS)
description: Naučte se řídit přístup pomocí PodSecurityPolicy ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: a9f6ead7edea7a3a6240e116d3073ea01fa9f6bb
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900108"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Preview – Zabezpečte svůj cluster pomocí zásad zabezpečení v Azure Kubernetes Service (AKS).

> [!WARNING]
> **Funkce popsaná v tomto dokumentu, pod zásadou zabezpečení (Preview), je nastavena pro vyřazení a nebude již k dispozici po 1. února 2021** ve prospěch [Azure Policy pro AKS](use-pod-security-on-azure-policy.md). Datum vyřazení bylo prodlouženo od předchozího dne 15. října 2020.
>
> Po použití zásady zabezpečení (Preview) je zastaralá. tuto funkci je třeba zakázat na všech stávajících clusterech pomocí zastaralé funkce, aby se prováděly budoucí upgrady clusteru a zůstaly v rámci podpory Azure.
>
> Důrazně doporučujeme začít s testováním scénářů pomocí Azure Policy pro AKS, což nabízí integrované zásady pro zabezpečení lusků a integrovaných iniciativ, které se mapují na zásady zabezpečení pod. Kliknutím sem se dozvíte víc o [migraci na Azure Policy ze zásady zabezpečení (Preview)](use-pod-security-on-azure-policy.md#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

Chcete-li zlepšit zabezpečení clusteru AKS, můžete omezit, které části je možné naplánovat. Lusky, které vyžadují prostředky, které nepovolíte, nejde spustit v clusteru AKS. Tento přístup definujete pomocí zásad zabezpečení pod. V tomto článku se dozvíte, jak používat zásady zabezpečení pod k omezení nasazení lusků v AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření rozhraní příkazového řádku aks-preview

Pokud chcete použít zásady zabezpečení pod, potřebujete rozšíření *AKS-Preview* CLI verze 0.4.1 nebo vyšší. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte, jestli nejsou dostupné aktualizace, pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrace pod poskytovatelem funkcí zásad zabezpečení

**Tento dokument a funkce se nastavují pro vyřazení 15. října 2020.**

Pokud chcete vytvořit nebo aktualizovat cluster AKS pro použití zásad zabezpečení pod, nejdřív Povolte ve svém předplatném příznak funkce. Chcete-li zaregistrovat příznak funkce *PodSecurityPolicyPreview* , použijte příkaz [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Zobrazení stavu v *registraci* trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Přehled zásad zabezpečení pod

V clusteru Kubernetes se k zachycení požadavků na server rozhraní API používá řadič pro přístup, když se prostředek vytvoří. Řadič pro přijímání pak může *ověřit* požadavek prostředku na základě sady *pravidel nebo podle* prostředku změnit parametry nasazení.

*PodSecurityPolicy* je řadič pro přijímání, který ověřuje specifikaci pod, splňuje vaše definované požadavky. Tyto požadavky mohou omezit použití privilegovaných kontejnerů, přístup k určitým typům úložiště nebo uživatele nebo skupiny, ve kterých může být kontejner spuštěn. Když se pokusíte nasadit prostředek, u kterého specifikace pod nesplňují požadavky uvedené v zásadách zabezpečení pod, požadavek se odepře. Tato možnost určuje, které lusky se můžou naplánovat v clusteru AKS, brání určitým možným chybám zabezpečení nebo zvýšení úrovně oprávnění.

Když v clusteru AKS zapnete zásadu zabezpečení pod, uplatní se některé výchozí zásady. Tyto výchozí zásady poskytují předem připravené možnosti, které definují, jaké lusky je možné naplánovat. Nicméně uživatelé clusteru můžou narazit na problémy s nasazováním lusků, dokud nedefinujete vlastní zásady. Doporučený postup je:

* Vytvoření clusteru AKS
* Definovat vlastní zásady zabezpečení pod
* Povolení funkce zásady zabezpečení pod

Pokud chcete zobrazit, jak výchozí zásady omezují podle nasazení, v tomto článku nejdřív povolíte funkci zásady zabezpečení pod a pak vytvoříte vlastní zásadu.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Povolit zásadu zabezpečení pod v clusteru AKS

Pomocí příkazu [AZ AKS Update][az-aks-update] můžete povolit nebo zakázat zásadu zabezpečení pod. Následující příklad povolí zásady zabezpečení pro název clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup* .

> [!NOTE]
> Pro reálné použití nepovolujte zásady zabezpečení pod, dokud nedefinujete vlastní zásady. V tomto článku aktivujete zásadu zabezpečení pod prvním krokem, abyste viděli, jak výchozí zásady omezují na pod nasazeními.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Výchozí zásady AKS

Když zapnete zásadu zabezpečení pod, AKS vytvoří jednu výchozí zásadu s názvem *Privileged* . Neupravujte ani neodstraňujte výchozí zásady. Místo toho vytvořte vlastní zásady, které definují nastavení, které chcete ovládat. Nejdřív se podíváme na to, jak tyto výchozí zásady ovlivňují nasazení pod.

Pokud chcete zobrazit dostupné zásady, použijte příkaz [kubectl Get PSP][kubectl-get] , jak je znázorněno v následujícím příkladu.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Zásady zabezpečení *Privileged* pod se aplikují na každého ověřeného uživatele v clusteru AKS. Toto přiřazení se řídí ClusterRoles a ClusterRoleBindings. Použijte příkaz [kubectl Get rolebindings][kubectl-get] a vyhledejte *výchozí: Privileged:* Binding v oboru názvů *Kube-System* :

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Jak je znázorněno v následujícím zhuštěném výstupu, je režim *PSP: Privileged* ClusterRole přiřazen všem *systémům: ověření* uživatelé. Tato možnost poskytuje základní úroveň oprávnění bez definování vlastních zásad.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Je důležité porozumět tomu, jak tyto výchozí zásady pracují s požadavky uživatelů na plánování lusků předtím, než začnete vytvářet vlastní zásady zabezpečení pod. V následujících částech plánujeme některé lusky, aby viděli tyto výchozí zásady v akci.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Vytvoření testovacího uživatele v clusteru AKS

Ve výchozím nastavení platí, že když použijete příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] , přidají se do konfigurace přihlašovací údaje *správce* pro cluster AKS `kubectl` . Uživatel s rolí správce obchází vynucování zásad zabezpečení pod. Pokud pro clustery AKS používáte integraci Azure Active Directory, můžete se přihlásit pomocí přihlašovacích údajů uživatele bez oprávnění správce, aby se zobrazilo vynucování zásad v akci. V tomto článku vytvoříme účet testovacího uživatele v clusteru AKS, který můžete použít.

Vytvořte ukázkový obor názvů s názvem *PSP-AKS* pro zdroje testu pomocí příkazu [kubectl Create Namespace][kubectl-create] . Pak vytvořte účet služby s názvem *neadmin-User* pomocí příkazu [kubectl Create ServiceAccount][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

V dalším kroku vytvořte RoleBinding pro *uživatele bez správce* , aby se v oboru názvů prováděly základní akce pomocí příkazu [kubectl Create RoleBinding][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Vytváření příkazů aliasu pro správce a uživatele bez role správce

Chcete-li zvýraznit rozdíl mezi běžným uživatelem s rolí správce při použití nástroje `kubectl` a uživatelem bez role správce vytvořeným v předchozích krocích, vytvořte dva aliasy příkazového řádku:

* Alias **kubectl-admin** je určen pro obvyklého uživatele správce a je vymezen na obor názvů *PSP-AKS* .
* Alias **kubectl-nonadminuser** je pro uživatele, který není *správce* vytvořený v předchozím kroku, a má obor názvů *PSP-AKS* .

Vytvořte tyto dva aliasy, jak je znázorněno v následujících příkazech:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testování vytvoření privilegovaného pod

Pojďme nejdřív otestovat, co se stane, když naplánujete pod, pomocí kontextu zabezpečení `privileged: true` . Tento kontext zabezpečení přestupňování oprávnění pod. V předchozí části, která ukázala výchozí zásady zabezpečení AKS pod, by měla zásada *oprávnění* zamítnout tuto žádost.

Vytvořte soubor s názvem `nginx-privileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Vytvořte pod pomocí příkazu [kubectl Applu][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

V části se nezdařila plánovaná, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

V poli se nedosáhnou fáze plánování, takže před přesunutím na neexistují žádné prostředky, které by bylo možné odstranit.

## <a name="test-creation-of-an-unprivileged-pod"></a>Vytvoření testu neprivilegovaného pod

V předchozím příkladu specifikace pod požaduje privilegovanou eskalaci. Tento požadavek je odepřený pomocí výchozích zásad zabezpečení *Privilege* , takže se u něj nepovede naplánovat. Pojďme teď spustit stejný NGINX pod tím, že nebudete mít požadavek na eskalaci oprávnění.

Vytvořte soubor s názvem `nginx-unprivileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Vytvořte pod pomocí příkazu [kubectl Applu][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

V části se nezdařila plánovaná, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

V poli se nedosáhnou fáze plánování, takže před přesunutím na neexistují žádné prostředky, které by bylo možné odstranit.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Vytvoření testu pod s konkrétním kontextem uživatele

V předchozím příkladu se image kontejneru automaticky pokusila použít kořen k navázání NGINX na port 80. Tato žádost byla zamítnutá pomocí výchozích zásad zabezpečení *Privilege* , takže se na začátku nespustí. Pojďme teď spustit stejný NGINX pod stejným kontextem uživatele, jako je třeba `runAsUser: 2000` .

Vytvořte soubor s názvem `nginx-unprivileged-nonroot.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Vytvořte pod pomocí příkazu [kubectl Applu][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

V části se nezdařila plánovaná, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

V poli se nedosáhnou fáze plánování, takže před přesunutím na neexistují žádné prostředky, které by bylo možné odstranit.

## <a name="create-a-custom-pod-security-policy"></a>Vytvořit vlastní zásadu zabezpečení pod

Teď, když jste se seznámili s chováním výchozích zásad zabezpečení pod, Pojďme dát *nesprávci* možnost, aby nedokázali naplánovat lusky.

Pojďme vytvořit zásadu, která odmítne lusky, které požadují privilegovaný přístup. Další možnosti, například *runAsUser* nebo povolené *svazky* , nejsou výslovně omezeny. Tento typ zásady odepře požadavek na privilegovaný přístup, ale jinak umožňuje clusteru spustit požadované lusky.

Vytvořte soubor s názvem `psp-deny-privileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Vytvořte zásadu pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Pokud chcete zobrazit dostupné zásady, použijte příkaz [kubectl Get PSP][kubectl-get] , jak je znázorněno v následujícím příkladu. Porovnejte zásadu *PSP-Deny-Privilege* s výchozími zásadami *oprávnění* , které byly vyhodnoceny v předchozích příkladech, a vytvořte pod ní. Zásady zakázaly jenom použití eskalace *priv* . Pro zásady *PSP-Deny-Privilege* neexistují žádná omezení pro uživatele nebo skupinu.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Povolí uživatelskému účtu používat vlastní zásady zabezpečení pod.

V předchozím kroku jste vytvořili zásadu zabezpečení pod tím, že odmítnete lusky, které požadují privilegovaný přístup. Pokud chcete, aby se tato zásada použila, vytvořte *roli* nebo *ClusterRole* . Pak přidružíte jednu z těchto rolí pomocí *RoleBinding* nebo *ClusterRoleBinding* .

V tomto příkladu vytvořte ClusterRole, který umožňuje *použít* zásadu *PSP-Deny-Privileged* vytvořenou v předchozím kroku. Vytvořte soubor s názvem `psp-deny-privileged-clusterrole.yaml` a vložte následující YAML manifest:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Vytvořte ClusterRole pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název vašeho manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Nyní vytvořte ClusterRoleBinding pro použití ClusterRole vytvořené v předchozím kroku. Vytvořte soubor s názvem `psp-deny-privileged-clusterrolebinding.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Vytvořte ClusterRoleBinding pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název vašeho manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> V prvním kroku tohoto článku byla funkce zásady zabezpečení pod povolena v clusteru AKS. Doporučeným postupem bylo povolit funkci zásady zabezpečení pod, jenom když jste definovali vlastní zásady. To je fáze, kde byste povolili funkci zásady zabezpečení pod. Byla definována jedna nebo více vlastních zásad a k těmto zásadám byly přidruženy uživatelské účty. Teď můžete bezpečně povolit funkci zásady zabezpečení pod a minimalizovat problémy způsobené výchozími zásadami.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Otestování opětovného vytvoření neprivilegovaného objektu.

Když použijete vlastní zásadu zabezpečení pod a vytvoříte vazbu pro uživatelský účet, abyste mohli zásady používat, zkusíme znovu vytvořit Neprivilegovaný příkaz. Pomocí stejného `nginx-privileged.yaml` manifestu vytvořte pod pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

V části se úspěšně naplánovalo. Když zkontrolujete stav pod, pomocí příkazu [kubectl Get lusky][kubectl-get] je *spuštěný* :

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Tento příklad ukazuje, jak můžete vytvořit vlastní zásady zabezpečení, které definují přístup ke clusteru AKS pro různé uživatele nebo skupiny. Výchozí zásady AKS poskytují těsné kontroly nad tím, jak se můžou lusky spouštět, takže vytvořte vlastní zásady, které pak správně definují potřebná omezení.

Pomocí příkazu [kubectl Delete][kubectl-delete] odstraňte Nginx s neprivilegovaným příkazem a zadejte název vašeho manifestu YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zakázat zásadu zabezpečení pod, použijte znovu příkaz [AZ AKS Update][az-aks-update] . Následující příklad zakáže zásady zabezpečení v názvu clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup* :

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Dále odstraňte ClusterRole a ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Odstraňte zásadu zabezpečení pomocí příkazu [kubectl Delete][kubectl-delete] a zadejte název manifestu YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Nakonec odstraňte obor názvů *PSP-AKS* :

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak vytvořit zásadu zabezpečení pod tím, abyste zabránili použití privilegovaného přístupu. Existuje spousta funkcí, které může zásada vyhovět, jako je například typ svazku nebo uživatel RunAs. Další informace o dostupných možnostech najdete v [referenční dokumentaci k zásadám zabezpečení Kubernetes pod][kubernetes-policy-reference].

Další informace o omezování síťového provozu najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě v AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
