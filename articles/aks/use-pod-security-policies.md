---
title: Použití zásad zabezpečení podu ve službě Azure Kubernetes Service (AKS)
description: Naučte se řídit přijetí podu pomocí PodSecurityPolicy ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998360"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Preview – zabezpečení clusteru pomocí zásad zabezpečení podu ve službě Azure Kubernetes Service (AKS)

Chcete-li zlepšit zabezpečení clusteru AKS, můžete omezit, jaké pody lze naplánovat. Pody, které požadují prostředky, které nepovolujete, nelze spustit v clusteru AKS. Tento přístup definujete pomocí zásad zabezpečení podu. Tento článek ukazuje, jak používat zásady zabezpečení pod uomezeníní nasazení podů v AKS.

> [!IMPORTANT]
> Funkce AKS preview jsou samoobslužné opt-in. Náhledy jsou poskytovány "tak, jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS Previews jsou částečně pokryty zákaznickou podporou na základě maximálního úsilí. Jako takové tyto funkce nejsou určeny pro produkční použití. Další informace naleznete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, podívejte se na aks rychlý start [pomocí Azure CLI][aks-quickstart-cli] nebo [pomocí portálu Azure][aks-quickstart-portal].

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.61 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření cli aks-preview

Chcete-li použít zásady zabezpečení pod, potřebujete *rozšíření aks-preview* CLI verze 0.4.1 nebo vyšší. Nainstalujte rozšíření *AKS-preview* Azure CLI pomocí příkazu [az extension add][az-extension-add] a pak zkontrolujte všechny dostupné aktualizace pomocí příkazu [aktualizace rozšíření az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Zaregistrovat zprostředkovatele funkcí zásad zabezpečení podu

Chcete-li vytvořit nebo aktualizovat cluster AKS tak, aby používal zásady zabezpečení podu, povolte nejprve příznak funkce v rámci předplatného. Chcete-li zaregistrovat příznak funkce *PodSecurityPolicyPreview,* použijte příkaz [az feature register,][az-feature-register] jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Když zaregistrujete funkci v předplatném, nelze tuto funkci aktuálně zrušit. Po povolení některých funkcí náhledu mohou být výchozí hodnoty použity pro všechny clustery AKS, které jsou poté vytvořeny v předplatném. Nepovolujte funkce náhledu v produkčních předplatných. Pomocí samostatného předplatného otestujte funkce náhledu a shromážděte zpětnou vazbu.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Trvá několik minut, než se zobrazí stav *Registrováno*. Stav registrace můžete zkontrolovat pomocí příkazu [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft.ContainerService* pomocí příkazu [registrovat zprostředkovatele az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Přehled zásad zabezpečení podu

V clusteru Kubernetes se řadič přijetí používá k zachycení požadavků na server rozhraní API při vytvoření prostředku. Řadič přijetí pak můžete *ověřit* požadavek na prostředek proti sadu pravidel nebo *mutovat* prostředek změnit parametry nasazení.

*PodSecurityPolicy* je řadič přijetí, který ověřuje specifikaci podu splňuje vaše definované požadavky. Tyto požadavky mohou omezit použití privilegovaných kontejnerů, přístup k určitým typům úložiště nebo uživateli nebo skupině, jako může kontejner spustit. Při pokusu o nasazení prostředku, kde specifikace podneste nesplňují požadavky popsané v zásadách zabezpečení pod, požadavek je odepřen. Tato schopnost řídit, jaké pody lze naplánovat v clusteru AKS zabraňuje některé možné slabá místa zabezpečení nebo eskalace oprávnění.

Pokud povolíte zásady zabezpečení pod v clusteru AKS, budou použity některé výchozí zásady. Tyto výchozí zásady poskytují předem uvedené prostředí k definování, jaké pody lze naplánovat. Uživatelé clusteru však může narazit na problémy nasazení podů, dokud nedefinujete vlastní zásady. Doporučeným přístupem je:

* Vytvoření clusteru AKS
* Definování vlastních zásad zabezpečení podu
* Povolení funkce zásad zabezpečení podu

Chcete-li zobrazit, jak výchozí zásady omezují nasazení podů, v tomto článku nejprve povolíme funkci zásad zabezpečení podu a pak vytvoříme vlastní zásady.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Povolení zásad zabezpečení podu v clusteru AKS

Zásady zabezpečení podu můžete povolit nebo zakázat pomocí příkazu [az aks update.][az-aks-update] Následující příklad povolí zásady zabezpečení podu v názvu clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*.

> [!NOTE]
> Pro skutečné použití nepovolujte zásady zabezpečení podu, dokud nedefinujete vlastní zásady. V tomto článku povolíte zásady zabezpečení pod jako první krok zobrazíte, jak výchozí zásady omezit nasazení pod.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Výchozí zásady AKS

Pokud povolíte zásady zabezpečení pod, AKS vytvoří jednu výchozí zásadu s názvem *privilegované*. Neupravujte ani neodstraňujte výchozí zásady. Místo toho vytvořte vlastní zásady, které definují nastavení, které chcete řídit. Nejprve se podíváme na to, jaké jsou tyto výchozí zásady, jak ovlivňují nasazení podů.

Chcete-li zobrazit dostupné zásady, použijte příkaz [kubectl get psp,][kubectl-get] jak je znázorněno v následujícím příkladu

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Zásady zabezpečení *privilegovaného* modulu se použijí na všechny ověřené uživatele v clusteru AKS. Toto přiřazení je řízeno clusterroles a clusterrolebindings. Použijte příkaz [kubectl get rolebindings][kubectl-get] a vyhledejte *výchozí:privileged:* vazbu v oboru názvů *kube-system:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Jak je znázorněno na následujícím zkráceném výstupu, *psp:restricted* ClusterRole je přiřazena všem *uživatelům system:authenticated.* Tato možnost poskytuje základní úroveň omezení bez definování vlastních zásad.

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

Je důležité pochopit, jak tyto výchozí zásady interagují s požadavky uživatelů na plánování podů, než začnete vytvářet vlastní zásady zabezpečení podu. V několika následujících částech naplánujeme některé pody tak, aby tyto výchozí zásady viděly v akci.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Vytvoření testovacího uživatele v clusteru AKS

Ve výchozím nastavení při použití příkazu [az aks get-credentials][az-aks-get-credentials] jsou do konfigurace `kubectl` přidána pověření *správce* pro cluster AKS. Uživatel správce obchází vynucení zásad zabezpečení podu. Pokud používáte integraci Služby Azure Active Directory pro clustery AKS, můžete se přihlásit pomocí přihlašovacích údajů uživatele, který není správcem, a zobrazit tak vynucení zásad v akci. V tomto článku vytvoříme testovací uživatelský účet v clusteru AKS, který můžete použít.

Vytvořte ukázkový obor názvů s názvem *psp-aks* pro testovací prostředky pomocí příkazu [create namespace kubectl.][kubectl-create] Potom vytvořte účet služby s názvem *nonadmin-user* pomocí [příkazu kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Dále vytvořte rolebinding pro uživatele, který *není admin,* k provádění základních akcí v oboru názvů pomocí příkazu [vytvořit rolebinding kubectl:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Vytvoření příkazů aliasu pro správce a uživatele, kteří nejsou správci

Chcete-li zvýraznit rozdíl `kubectl` mezi běžným uživatelem správce při použití a uživatelem, který není správcem, vytvořeným v předchozích krocích, vytvořte dva aliasy příkazového řádku:

* Alias **kubectl-admin** je určen pro běžného administrátora a je vymezen do oboru *psp-aks* oboru.
* Alias **kubectl-nonadminuser** je určen pro *uživatele, který není admin- uživatel* vytvořený v předchozím kroku, a je vymezen do oboru názvů *psp-aks.*

Vytvořte tyto dva aliasy, jak je znázorněno v následujících příkazech:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Otestujte vytvoření privilegovaného modulu

Nejprve otestujeme, co se stane, když `privileged: true`naplánujete pod s kontextem zabezpečení aplikace . Tento kontext zabezpečení eskaluje oprávnění modulu. V předchozí části, která ukázala výchozí zásady zabezpečení pod AKS, by zásady *s omezeným přístupem* měly tento požadavek odepřít.

Vytvořte soubor `nginx-privileged.yaml` s názvem a vložte následující manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Vytvořte pod pomocí [příkazu kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod se nezdaří naplánovat, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod nedosáhne fáze plánování, takže před přechodem nejsou k dispozici žádné prostředky, které by bylo možné odstranit.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testování vytvoření neprivilegovaného podu

V předchozím příkladu specifikace podu požadovala privilegované eskalace. Tento požadavek je odmítnut výchozí zásady zabezpečení *podu s omezeným* přístupem, takže pod se nezdaří naplánovat. Zkusme nyní spustit stejný pod NGINX bez žádosti o eskalaci oprávnění.

Vytvořte soubor `nginx-unprivileged.yaml` s názvem a vložte následující manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Vytvořte pod pomocí [příkazu kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod se nezdaří naplánovat, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod nedosáhne fáze plánování, takže před přechodem nejsou k dispozici žádné prostředky, které by bylo možné odstranit.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testování vytvoření podu s určitým uživatelským kontextem

V předchozím příkladu image kontejneru automaticky pokusil použít root svázat NGINX na port 80. Tento požadavek byl odmítnut výchozí zásady zabezpečení *pod u omezeného* modulu, takže pod se nezdaří spustit. Zkusme nyní spustit stejný pod NGINX s určitým uživatelským kontextem, například `runAsUser: 2000`.

Vytvořte soubor `nginx-unprivileged-nonroot.yaml` s názvem a vložte následující manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Vytvořte pod pomocí [příkazu kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Pod se nezdaří naplánovat, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod nedosáhne fáze plánování, takže před přechodem nejsou k dispozici žádné prostředky, které by bylo možné odstranit.

## <a name="create-a-custom-pod-security-policy"></a>Vytvoření vlastní zásady zabezpečení podu

Teď, když jste viděli chování výchozí pod zásady zabezpečení, pojďme poskytnout způsob, jak *pro nonadmin-uživatel* úspěšně naplánovat pody.

Pojďme vytvořit zásadu odmítnout pody, které požadují privilegovaný přístup. Jiné možnosti, například *runAsUser* nebo povolené svazky , nejsou explicitně *omezeny.* Tento typ zásad y odepře požadavek na privilegovaný přístup, ale jinak umožní clusteru spustit požadované pody.

Vytvořte soubor `psp-deny-privileged.yaml` s názvem a vložte následující manifest YAML:

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

Vytvořte zásadu pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Chcete-li zobrazit dostupné zásady, použijte příkaz [kubectl get psp,][kubectl-get] jak je znázorněno v následujícím příkladu. Porovnejte *psp odepřít privilegované* zásady s výchozí *omezené* zásady, která byla vynucena v předchozích příkladech k vytvoření pod. Vaše zásady popírají pouze použití eskalace *PRIV.* Neexistují žádná omezení pro uživatele nebo skupinu pro *zásady psp odepřít privilegované.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Povolit uživatelskému účtu používat zásady zabezpečení vlastního modulu

V předchozím kroku jste vytvořili zásady zabezpečení podu odmítnout pody, které požadují privilegovaný přístup. Chcete-li povolit použití zásady, vytvořte *roli* nebo *clusterrole*. Potom přidružíte jednu z těchto rolí pomocí *RoleBinding* nebo *ClusterRoleBinding*.

V tomto příkladu vytvořte ClusterRole, který umožňuje *použít* *psp odepřít privilegované zásady* vytvořené v předchozím kroku. Vytvořte soubor `psp-deny-privileged-clusterrole.yaml` s názvem a vložte následující manifest YAML:

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

Vytvořte clusterrole pomocí [příkazu kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Nyní vytvořte clusterrolebinding pro použití ClusterRole vytvořené v předchozím kroku. Vytvořte soubor `psp-deny-privileged-clusterrolebinding.yaml` s názvem a vložte následující manifest YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Vytvořte clusterrolebinding pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> V prvním kroku tohoto článku byla v clusteru AKS povolena funkce zásad zabezpečení podu. Doporučeným postupem bylo povolit funkci zásad zabezpečení podu až po definování vlastních zásad. Toto je fáze, kdy byste povolili funkci zásad zabezpečení podu. Byla definována jedna nebo více vlastních zásad a uživatelské účty byly přidruženy k těmto zásadám. Nyní můžete bezpečně povolit funkci zásad zabezpečení podu a minimalizovat problémy způsobené výchozími zásadami.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Otestujte znovu vytvoření neprivilegovaného modulu

S vlastní pod zásady zabezpečení použít a vazbu pro uživatelský účet k použití zásady, pokusme se znovu vytvořit neprivilegované pod. Stejný `nginx-privileged.yaml` manifest použijte k vytvoření podu pomocí příkazu [použít kubectl:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod je úspěšně naplánováno. Když zaškrtnete stav podu pomocí příkazu [kubectl get pods,][kubectl-get] pod je *Spuštěno*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Tento příklad ukazuje, jak můžete vytvořit vlastní zásady zabezpečení pod udefinovat přístup k clusteru AKS pro různé uživatele nebo skupiny. Výchozí zásady AKS poskytují přísné ovládací prvky na co pody lze spustit, takže vytvořte vlastní zásady pak správně definovat omezení, která potřebujete.

Odstraňte neprivilegovaný pod NGINX pomocí příkazu [kubectl delete][kubectl-delete] a zadejte název manifestu YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li zakázat zásady zabezpečení pod, použijte příkaz [az aks update][az-aks-update] znovu. Následující příklad zakáže zásady zabezpečení podu v názvu clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Dále odstraňte clusterrole a clusterrolebinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Odstraňte zásady zabezpečení pomocí [příkazu kubectl delete][kubectl-delete] a zadejte název manifestu YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Nakonec odstraňte obor názvů *psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Další kroky

Tento článek vám ukázal, jak vytvořit zásady zabezpečení pod uzabránit použití privilegovaného přístupu. Existuje mnoho funkcí, které zásady lze vynutit, jako je například typ svazku nebo uživatel RunAs. Další informace o dostupných možnostech naleznete v [dokumentech s odkazy na zásady zabezpečení kubernetes .][kubernetes-policy-reference]

Další informace o omezení síťového provozu podů naleznete v [tématu Zabezpečení provozu mezi pody pomocí zásad sítě v AKS][network-policies].

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
