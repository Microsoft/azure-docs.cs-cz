---
title: Použít zásady zabezpečení pod ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak řídit pod nemocnicích pomocí PodSecurityPolicy ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 7ce311ab9c554481f64c6c9be40e2018893a0966
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013608"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Ve verzi Preview – zabezpečení clusteru pomocí pod zásad zabezpečení ve službě Azure Kubernetes Service (AKS)

Pokud chcete zlepšit zabezpečení clusteru AKS, můžete omezit podů, může být naplánované. Podů, které požadavky na prostředky, které nechcete povolit nelze spustit v clusteru AKS. Můžete definovat zásady zabezpečení pod tento přístup. V tomto článku se dozvíte, jak používat zásady zabezpečení pod omezit nasazení podů ve službě AKS.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb a vyjádřit výslovný souhlas. Verze Preview jsou k dispozici pro shromažďování zpětné vazby a chyb z naší komunitě. Však nepodporují technickou podporu Azure. Pokud vytvoříte cluster, nebo přidejte tyto funkce do existujících clusterů, se tento cluster nepodporuje, dokud tato funkce už je ve verzi preview a přechází do všeobecné dostupnosti (GA).
>
> Pokud narazíte na problémy s funkcemi ve verzi preview, [otevřete problém v úložišti Githubu AKS] [ aks-github] s názvem funkce ve verzi preview v název chyby.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS. Pokud potřebujete AKS cluster, najdete v tomto rychlém startu AKS [pomocí Azure CLI] [ aks-quickstart-cli] nebo [pomocí webu Azure portal][aks-quickstart-portal].

Musí mít Azure CLI verze 2.0.61 nebo později nainstalována a nakonfigurována. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření aks ve verzi preview rozhraní příkazového řádku

Clustery AKS jsou aktualizované, aby podporovaly pomocí zásad zabezpečení pod *aks ve verzi preview* rozšíření rozhraní příkazového řádku. Nainstalujte *aks ve verzi preview* pomocí rozšíření Azure CLI [přidat rozšíření az] [ az-extension-add] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Pokud jste dříve nainstalovali *aks ve verzi preview* rozšíření, nainstalujte všechny dostupné aktualizace pomocí `az extension update --name aks-preview` příkazu.

### <a name="register-pod-security-policy-feature-provider"></a>Zaregistrovat poskytovatele funkce zásad zabezpečení pod

Vytvořit nebo aktualizovat cluster AKS, pokud chcete používat zásady zabezpečení pod, povolte příznak funkce v rámci předplatného. K registraci *PodSecurityPolicyPreview* příznak funkce, použijte [az funkce register] [ az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Trvá několik minut, než se stav zobrazíte *registrované*. Vy můžete zkontrolovat stav registrace pomocí [seznam funkcí az] [ az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Až to budete mít, aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register] [ az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Přehled zásad zabezpečení pod

V clusteru Kubernetes, který se používá řadič jejich příchodu účelem zachycení požadavků na serveru rozhraní API, když prostředek se má vytvořit. Přijetí řadiče můžete pak *ověření* prostředků požadavku na sadu pravidel, nebo *mutovat* prostředku můžete změnit parametry nasazení.

*PodSecurityPolicy* je řadič přijetí, která ověřuje specifikace pod splňuje vaše požadavky definované. Tyto požadavky může omezit použití privileged kontejnery, přístup k určité typy úložiště, nebo uživatele nebo skupiny, které kontejner může spustit jako. Při pokusu o nasazení prostředku kde specifikace pod nesplňují požadavky uvedené v zásadách zabezpečení pod, požadavek se zamítne. Tato možnost řídit podů, může být naplánována AKS clusteru brání, některé chyby je to možné zabezpečení nebo eskalaci oprávnění.

Když povolíte zásady zabezpečení pod v clusteru AKS, použijí se některé výchozí zásady. Tyto výchozí zásady poskytují out-of-the-box prostředí k definování podů co je možné naplánovat. Ale clusteru mohou uživatelé o problémech s nasazením podů, dokud definovat vlastní zásady. Tento přístup doporučuje je:

* Vytvoření clusteru AKS
* Můžete definovat vlastní zásady zabezpečení pod
* Povolit funkci pod zásad zabezpečení

Chcete-li zobrazit jak výchozí zásady omezení pod nasazení, v tomto článku jsme nejprve povolit funkci zásady zabezpečení pod a potom vytvořte vlastní zásadu.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Povolit zásady zabezpečení pod u clusteru AKS

Můžete povolit nebo zakázat pomocí zásad zabezpečení pod [az aks aktualizovat] [ az-aks-update] příkazu. Následující příklad povolí pod zásady zabezpečení na název clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*.

> [!NOTE]
> Pro použití reálného světa, není pod zásadu zabezpečení povolit dokud jste definovali vlastní zásady. V tomto článku se povolit zásady zabezpečení pod prvním krokem, pokud chcete zobrazit, jak omezit pod výchozí zásady nasazení.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Výchozí zásady AKS

Když povolíte zásady zabezpečení pod, AKS vytvoří dvě výchozí zásady s názvem *privilegovaných* a *s omezeným přístupem*. Není upravit nebo odebrat tyto výchozí zásady. Místo toho vytvořte vlastní zásady, které definují nastavení, které chcete do ovládacího prvku. První pohled na tyto výchozí zásady teď jsou, jak by mohly mít dopad pod nasazení.

Chcete-li zobrazit dostupné zásady, použijte [kubectl get psp] [ kubectl-get] příkaz, jak je znázorněno v následujícím příkladu. Jako součást výchozí *s omezeným přístupem* zásad, uživateli je zakázán *PRIV* privilegovaných pod Eskalace a uživatel *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*s omezeným přístupem* pod zabezpečení zásady platí pro každý ověřený uživatel v clusteru AKS. Toto přiřazení je řízena ClusterRoles a ClusterRoleBindings. Použití [kubectl get clusterrolebindings] [ kubectl-get] příkaz a vyhledejte *výchozí: s omezeným přístupem:* vazby:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Jak je znázorněno v následující zkrácený výstup *psp: s omezeným přístupem* ClusterRole je přiřazený k žádné *systému: ověření* uživatelů. Tato možnost poskytuje základní úroveň omezení bez svoje vlastní zásady definované.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Je důležité pochopit, jak tyto zásady výchozí pracovat s uživatelskými požadavky naplánování podů předtím, než začnete vytvářet své vlastní pod zásady zabezpečení. V následujících částech Pojďme naplánovat některé podů, chcete-li zobrazit tyto výchozí zásady v akci.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Vytvoření zkušebního uživatele v clusteru AKS

Ve výchozím nastavení při použití [az aks get-credentials] [ az-aks-get-credentials] příkazu *správce* přihlašovací údaje pro AKS cluster a přidán do vaší `kubectl` config. Uživatel s rolí správce obchází vynucení zásad zabezpečení pod. Pokud používání integrace služby Azure Active Directory pro své clustery AKS, může zobrazit vynucení zásad v akci Přihlaste se pomocí přihlašovacích údajů uživatele bez oprávnění správce. V tomto článku vytvoříte testovací uživatelský účet v clusteru AKS, který vám pomůže.

Vytvoření ukázkové oboru názvů s názvem *psp aks* pro testovací prostředky pomocí [kubectl vytvoření oboru názvů] [ kubectl-create] příkazu. Vytvořte účet služby s názvem *text nonadmin uživatele* pomocí [kubectl vytvořit serviceaccount] [ kubectl-create] příkaz:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Dále vytvořte RoleBinding pro *text nonadmin uživatele* provádět základní akce s použitím oboru názvů [kubectl vytvořit rolebinding] [ kubectl-create] příkaz:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Vytvořit příkazy alias pro uživatele správce nebo bez oprávnění správce.

Abyste měli na očích rozdíl mezi uživatele s rolí správce regulární při použití `kubectl` a uživatele bez oprávnění správce vytvořený v předchozích krocích vytvoříte dvě aliasy příkazového řádku:

* **Kubectl správce** alias je pro uživatele s rolí správce pravidelné a je vymezen *psp aks* oboru názvů.
* **Kubectl nonadminuser** je alias pro *text nonadmin uživatele* vytvořili v předchozím kroku a je vymezen *psp aks* oboru názvů.

Vytvořte tyto dva aliasy, jak je znázorněno v následujících příkazech:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testování vytvoření privilegovaných pod

Pojďme nejdříve testování co se stane, když naplánujete podů s kontextem zabezpečení `privileged: true`. Takový kontext zabezpečení proto ho postoupí pod oprávnění. V předchozí části, které jsme si ukázali AKS pod výchozí zásady zabezpečení *s omezeným přístupem* zásad by měl zamítněte tuto žádost.

Vytvořte soubor s názvem `nginx-privileged.yaml` a vložte následující YAML manifestu:

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

Vytvoření s použitím pod [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod nepodaří naplánovat, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod nemá přístup fázi plánování, takže není k dispozici žádné prostředky odstranit a teprve potom se přesunete.

## <a name="test-creation-of-an-unprivileged-pod"></a>Vytváření testů bez oprávnění umožňovala zvlášť pod

V předchozím příkladu si vyžádal specifikaci pod zvýšení úrovně oprávnění. Tento požadavek se zamítne ve výchozím nastavení *s omezeným přístupem* pod zásady zabezpečení, takže pod selže k naplánování. Zkusme teď běží tohoto stejného podu NGINX bez žádosti o eskalaci oprávnění.

Vytvořte soubor s názvem `nginx-unprivileged.yaml` a vložte následující YAML manifestu:

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

Vytvoření s použitím pod [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Plánovač Kubernetes přijme žádost pod. Ale pokud byste se podívat na stav pod pomocí `kubectl get pods`, dojde k chybě:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Použití [kubectl popisují pod] [ kubectl-describe] příkaz podívat se na události pod. Následujícímu zhuštěnému příkladu ukazuje, že kontejner a bitové kopie vyžadují kořenová oprávnění, i v případě, že jsme neměli o ně požádat:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

I v případě, že jsme informace nevyžádali privilegovaný přístup, je potřeba vytvořit vazbu pro port image kontejneru pro server NGINX *80*. K vytvoření vazby porty *1024* a nižší, *kořenové* uživatel je vyžadován. Pokud chcete pod pokusí spustit, *s omezeným přístupem* zásady zabezpečení pod zakazuje této žádosti.

Tento příklad ukazuje, že zásady zabezpečení pod výchozí vytvořené službou AKS jsou aktivní a omezit akce, které může uživatel provést. Je důležité pochopit chování těchto výchozích zásad, tak, jak očekáváte nemusí základní NGINX pod kterým bude odepřen.

Teprve potom přejděte další krok odstranit pod tento test pomocí [kubectl odstranit pod] [ kubectl-delete] příkaz:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Vytváření testů pod s kontextu konkrétního uživatele

V předchozím příkladu se image kontejneru automaticky pokusili použít kořenovém NGINX vytvořit vazbu s portem 80. Tato žádost byla zamítnuta ve výchozím nastavení *s omezeným přístupem* pod zásady zabezpečení, takže pod nepodaří spustit. Pojďme si vyzkoušet aktuálně spuštěno tohoto podu stejný server NGINX s kontextu konkrétního uživatele, jako například `runAsUser: 2000`.

Vytvořte soubor s názvem `nginx-unprivileged-nonroot.yaml` a vložte následující YAML manifestu:

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

Vytvoření s použitím pod [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Plánovač Kubernetes přijme žádost pod. Ale pokud byste se podívat na stav pod pomocí `kubectl get pods`, dochází k chybě jiné než v předchozím příkladu:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Použití [kubectl popisují pod] [ kubectl-describe] příkaz podívat se na události pod. Následujícímu zhuštěnému příkladu zobrazí pod události:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Události, které označují, že kontejner byl vytvořen a spustit. Není co hned zjevné, proč chcete pod v chybovém stavu. Pojďme se podívat na protokoly pod pomocí [kubectl protokoly] [ kubectl-logs] příkaz:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

Následující příklad výstupu protokolu poskytuje jako ukazatel toho, který v rámci samotného konfigurace serveru NGINX, služba se pokusí spustit dochází k chybě oprávnění. Tato chyba je způsobena znovu museli vytvořit vazbu na port 80. I když specifikaci pod definované běžný uživatelský účet, není tento uživatelský účet dostatečná v úrovni operačního systému – službu NGINX spuštění a vytvořit vazbu na port s omezeným přístupem.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Znovu je důležité porozumět chování výchozí zásady zabezpečení pod. Tato chyba se trochu obtížnější sledování a znovu, nemusí očekáváte, že základní NGINX pod kterým bude odepřen.

Teprve potom přejděte další krok odstranit pod tento test pomocí [kubectl odstranit pod] [ kubectl-delete] příkaz:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Vytvoření zásad zabezpečení vlastní pod

Teď, když už víte, chování výchozí zásady zabezpečení pod, poskytují způsob, jak Dejme *text nonadmin uživatele* úspěšně naplánovat pody.

Pojďme vytvořit zásadu, která odmítnout podů, které vyžadují privilegovaný přístup. Další možnosti, jako například *Spustit_jako_uživatel* nebo povolené *svazky*, nejsou explicitně s omezeným přístupem. Tento typ zásad odmítne žádost o privilegovaný přístup, ale jinak umožní clusteru spustit požadovaný tyto pody.

Vytvořte soubor s názvem `psp-deny-privileged.yaml` a vložte následující YAML manifestu:

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

Vytvoření s použitím zásad [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Chcete-li zobrazit dostupné zásady, použijte [kubectl get psp] [ kubectl-get] příkaz, jak je znázorněno v následujícím příkladu. Porovnání *psp odepření oprávnění* zásady s výchozím *s omezeným přístupem* zásady, které bylo vynuceno v předchozích příkladech vytvoření pod. Použití pouze *PRIV* eskalace odepřen ve vašich zásadách. Neexistují žádná omezení na uživatele nebo skupinu pro *psp odepření oprávnění* zásad.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Povolit účet uživatele chcete použít vlastní pod zásady zabezpečení

V předchozím kroku jste vytvořili zásadu zabezpečení pod odmítnout podů, které požadavek privilegovaný přístup. Povolit zásady, který se má použít, vytvořte *Role* nebo *ClusterRole*. Pak přidružit jeden z těchto rolí pomocí *RoleBinding* nebo *ClusterRoleBinding*.

V tomto příkladu vytvoření ClusterRole, který vám umožní *použít* *psp odepření oprávnění* zásady vytvořili v předchozím kroku. Vytvořte soubor s názvem `psp-deny-privileged-clusterrole.yaml` a vložte následující YAML manifestu:

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

Vytvoření s použitím ClusterRole [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Teď vytvořte ClusterRoleBinding používat ClusterRole vytvořili v předchozím kroku. Vytvořte soubor s názvem `psp-deny-privileged-clusterrolebinding.yaml` a vložte následující YAML manifestu:

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

Vytvoření pomocí ClusterRoleBinding [použití kubectl] [ kubectl-apply] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> V prvním kroku v tomto článku byla povolena funkce zásad zabezpečení pod v clusteru AKS. Doporučeným postupem je jenom povolit funkci zásad zabezpečení pod po definování vlastní zásady. To je fáze, ve kterém byste měli povolit funkce zásad zabezpečení pod. Definovali jeden nebo více vlastních zásad a uživatelské účty byly přidružené k těmto zásadám. Nyní můžete bezpečně zásady zabezpečení pod běží na procesorech a minimalizovat potíže způsobené aktivitami výchozích zásad.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testování bez oprávnění umožňovala zvlášť pod vytvoření znovu

Použít vlastní pod zásady zabezpečení a vazby pro uživatelský účet na zásady Zkusme znovu vytvořit bez oprávnění umožňovala zvlášť pod. Použijte stejný `nginx-privileged.yaml` manifestu vytvořit pomocí pod [použití kubectl] [ kubectl-apply] příkaz:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod se úspěšně naplánovala. Při kontrole stavu pod pomocí [kubectl get pods] [ kubectl-get] příkazu je pod *systémem*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Tento příklad ukazuje, jak můžete vytvořit vlastní pod zásady zabezpečení definují přístup k clusteru AKS pro různé uživatele nebo skupiny. Výchozí zásady AKS poskytují přísnou podů, které lze spustit, aby vytvářet vlastní zásady správně definovat omezení, které potřebujete.

Odstranit pomocí NGINX Neprivilegovaný pod [kubectl odstranit] [ kubectl-delete] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li zakázat pod zásady zabezpečení, použijte [az aks aktualizovat] [ az-aks-update] příkaz znovu. Následující příklad zakazuje pod zásady zabezpečení na název clusteru *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

V dalším kroku odstraňte ClusterRole a ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Odstranit zásady sítě pomocí [kubectl odstranit] [ kubectl-delete] příkaz a zadejte název vašeho YAML manifestu:

```console
kubectl delete -f psp-deny-privileged.yaml
```

A konečně, odstranit *psp aks* obor názvů:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Další postup

Tento článek vám ukázali, jak vytvořit zásadu zabezpečení pod zabránit používání privilegovaný přístup. Existuje mnoho funkcí, které můžou vynutit zásadu, jako je například typ svazku nebo uživatele RunAs. Další informace o dostupných možnostech najdete v tématu [Kubernetes pod referenční dokumenty zásad zabezpečení][kubernetes-policy-reference].

Další informace o omezení síťového provozu pod najdete v tématu [zabezpečení přenosu mezi pody pomocí zásady sítě ve službě AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
[aks-github]: https://github.com/azure/aks/issues

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
