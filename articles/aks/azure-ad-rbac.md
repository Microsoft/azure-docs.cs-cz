---
title: Řízení prostředků clusteru pomocí RBAC a Azure AD ve službě Azure Kubernetes Service
description: Zjistěte, jak pomocí členství ve skupinách Azure Active Directory omezit přístup k prostředkům clusteru pomocí řízení přístupu na základě rolí (RBAC) ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: 456b6dcdd590b48e06c830db85b726d4bebb69e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596517"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Řízení přístupu k prostředkům clusteru pomocí řízení přístupu na základě rolí a identit Azure Active Directory ve službě Azure Kubernetes Service

Azure Kubernetes Service (AKS) lze nakonfigurovat tak, aby používala Azure Active Directory (AD) pro ověřování uživatelů. V této konfiguraci se přihlásíte ke clusteru AKS pomocí ověřovacího tokenu Azure AD. Můžete také nakonfigurovat řízení přístupu na základě rolí Kubernetes (RBAC) omezit přístup k prostředkům clusteru na základě identity uživatele nebo členství ve skupině.

Tento článek ukazuje, jak používat členství ve skupině Azure AD k řízení přístupu k oborům názvů a prostředkům clusteru pomocí Kubernetes RBAC v clusteru AKS. Ukázkové skupiny a uživatelé jsou vytvořeny ve službě Azure AD, pak role a rolebindings jsou vytvořeny v clusteru AKS udělit příslušná oprávnění k vytvoření a zobrazení prostředků.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS povolena s integrací Azure AD. Pokud potřebujete cluster AKS, [přečtěte si článek Integrace služby Azure Active Directory s AKS][azure-ad-aks-cli].

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Vytváření ukázkových skupin ve službě Azure AD

V tomto článku pojďme vytvořit dvě uživatelské role, které lze použít k zobrazení, jak Kubernetes RBAC a Azure AD řídit přístup k prostředkům clusteru. Používají se následující dvě příkladové role:

* **Vývojář aplikací**
    * Uživatel s názvem *aksdev,* který je součástí skupiny *appdev.*
* **Inženýr spolehlivosti webu**
    * Uživatel s názvem *akssre,* který je součástí *skupiny opssre.*

V produkčních prostředích můžete použít stávající uživatele a skupiny v rámci klienta Azure AD.

Nejprve získejte ID prostředku clusteru AKS pomocí příkazu [az aks show.][az-aks-show] Přiřaďte ID prostředku proměnné s názvem *AKS_ID,* aby na něj bylo možné odkazovat v dalších příkazech.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Vytvořte první ukázkovou skupinu ve službě Azure AD pro vývojáře aplikací pomocí příkazu [az az ad group create.][az-ad-group-create] Následující příklad vytvoří skupinu s názvem *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Nyní vytvořte přiřazení role Azure pro skupinu *appdev* pomocí příkazu [az role create.][az-role-assignment-create] Toto přiřazení umožňuje každému členovi skupiny pracovat `kubectl` s clusterem AKS tím, že jim udělí roli uživatele *clusteru služby Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Pokud se zobrazí chyba, jako `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`je například , počkejte několik sekund pro ID objektu skupiny Azure AD k šíření prostřednictvím adresáře a zkuste `az role assignment create` příkaz znovu.

Vytvořte druhou ukázkovou skupinu, tuto pro SRs s názvem *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Znovu vytvořte přiřazení role Azure, které uděluje členům *skupiny roli uživatele clusteru služby Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Vytváření uživatelů ukázkových ukázněných objektů ve službě Azure AD

Se dvěma ukázkovými skupinami vytvořenými ve službě Azure AD pro naše vývojáře aplikací a sres teď umožňuje vytvořit dva příklady uživatelů. Chcete-li otestovat integraci RBAC na konci článku, přihlaste se ke clusteru AKS s těmito účty.

Vytvořte první uživatelský účet ve službě Azure AD pomocí příkazu [vytvořit uživatele az reklamy.][az-ad-user-create]

Následující příklad vytvoří uživatele se zobrazovaným názvem *AKS Dev* a hlavním `aksdev@contoso.com`jménem uživatele (UPN) společnosti . Aktualizujte hlavní název podniku, aby zahrnoval ověřenou *contoso.com* doménu pro vašeho klienta `--password` Azure AD (nahraďte contoso.com vlastní doménou) a zadejte vlastní zabezpečené přihlašovací údaje:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Nyní přidejte uživatele do skupiny *appdev* vytvořené v předchozí části pomocí příkazu [přidat člen az ad group:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Vytvořte druhý uživatelský účet. Následující příklad vytvoří uživatele se zobrazovaným názvem *AKS SRE* a hlavním `akssre@contoso.com`jménem uživatele (UPN) . Znovu aktualizujte hlavní název stránky, aby zahrnoval ověřenou *contoso.com* doménu pro vašeho klienta Azure `--password` AD (nahraďte contoso.com vlastní doménou) a zadejte vlastní zabezpečené přihlašovací údaje:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Vytvoření prostředků clusteru AKS pro správce aplikací

Skupiny azure a uživatelé Azure AD jsou nyní vytvořeny. Přiřazení rolí Azure byly vytvořeny pro členy skupiny pro připojení ke clusteru AKS jako běžný uživatel. Nyní nakonfigurujeme cluster AKS tak, aby těmto různým skupinám umožňoval přístup k určitým prostředkům.

Nejprve získejte přihlašovací údaje správce clusteru pomocí příkazu [az aks get-credentials.][az-aks-get-credentials] V jedné z následujících částí získáte pověření běžného *clusteru uživatelů,* abyste viděli tok ověřování Azure AD v akci.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Vytvořte obor názvů v clusteru AKS pomocí příkazu [vytvořit obor názvů kubectl.][kubectl-create] Následující příklad vytvoří název oboru názvů *dev*:

```console
kubectl create namespace dev
```

V Kubernetes *role* definovat oprávnění k udělení a *RoleBindings* použít pro požadované uživatele nebo skupiny. Tato přiřazení lze použít pro daný obor názvů nebo v celém clusteru. Další informace naleznete [v tématu Použití autorizace RBAC][rbac-authorization].

Nejprve vytvořte roli pro obor názvů *dev.* Tato role uděluje úplným oprávněním oboru názvů. V produkčním prostředí můžete zadat podrobnější oprávnění pro různé uživatele nebo skupiny.

Vytvořte soubor `role-dev-namespace.yaml` s názvem a vložte následující manifest YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Vytvořte roli pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Dále získáte ID prostředku pro skupinu *appdev* pomocí příkazu [az ad group show.][az-ad-group-show] Tato skupina je nastavena jako předmět RoleBinding v dalším kroku.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Nyní vytvořte RoleBinding pro skupinu *appdev* použít dříve vytvořenou roli pro přístup k oboru názvů. Vytvořte soubor `rolebinding-dev-namespace.yaml` s názvem a vložte následující manifest YAML. Na posledním řádku nahraďte *groupObjectId* výstupem ID objektu skupiny z předchozího příkazu:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Vytvořte rolebinding pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Vytvoření prostředků clusteru AKS pro malé a střední podniky

Nyní opakujte předchozí kroky k vytvoření oboru názvů, role a rolebinding pro SRE.

Nejprve vytvořte obor názvů pro *sre* pomocí příkazu [kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Vytvořte soubor `role-sre-namespace.yaml` s názvem a vložte následující manifest YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Vytvořte roli pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Získejte ID zdroje pro skupinu *opssre* pomocí příkazu [az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Vytvořte rolebinding pro skupinu *opssre* pro použití dříve vytvořené role pro přístup k oboru názvů. Vytvořte soubor `rolebinding-sre-namespace.yaml` s názvem a vložte následující manifest YAML. Na posledním řádku nahraďte *groupObjectId* výstupem ID objektu skupiny z předchozího příkazu:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Vytvořte rolebinding pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interakce s prostředky clusteru pomocí identit Azure AD

Nyní otestujte očekávaná oprávnění při vytváření a správě prostředků v clusteru AKS. V těchto příkladech naplánujete a zobrazíte pody v přiděleném oboru názvů uživatele. Potom se pokusíte naplánovat a zobrazit pody mimo přiřazený obor názvů.

Nejprve obnovte kontext *kubeconfig* pomocí příkazu [az aks get-credentials.][az-aks-get-credentials] V předchozí části nastavíte kontext pomocí přihlašovacích údajů správce clusteru. Uživatel správce obchází výzvy přihlášení Azure AD. Bez `--admin` parametru se použije uživatelský kontext, který vyžaduje, aby byly všechny požadavky ověřeny pomocí služby Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Naplánujte základní pod NGINX pomocí příkazu [kubectl run][kubectl-run] v oboru názvů *dev:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Jako výzvu k přihlášení zadejte přihlašovací `appdev@contoso.com` údaje pro svůj vlastní účet vytvořený na začátku článku. Po úspěšném přihlášení je token účtu uložen do `kubectl` mezipaměti pro budoucí příkazy. NGINX je úspěšně naplánovat, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Nyní použijte [příkaz kubectl get pods][kubectl-get] k zobrazení podů v oboru názvů *dev.*

```console
kubectl get pods --namespace dev
```

Jak je znázorněno v následujícím příkladu výstupu, pod NGINX je úspěšně *spuštěn*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Vytvoření a zobrazení prostředků clusteru mimo přiřazený obor názvů

Nyní zkuste zobrazit pody mimo obor názvů *dev.* Použijte [příkaz kubectl get pods][kubectl-get] znovu, `--all-namespaces` tentokrát zobrazíte následující:

```console
kubectl get pods --all-namespaces
```

Členství uživatele ve skupině nemá roli Kubernetes, která umožňuje tuto akci, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Stejným způsobem zkuste naplánovat pod v jiném oboru názvů, jako je například obor názvů *sre.* Členství uživatele ve skupině není zarovnáno s rolí Kubernetes a RoleBinding, aby udělilo tato oprávnění, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testování přístupu SRE k prostředkům clusteru AKS

Chcete-li potvrdit, že naše členství ve skupině Azure AD a Kubernetes RBAC fungují správně mezi různými uživateli a skupinami, zkuste předchozí příkazy při přihlášení jako uživatel *opssre.*

Obnovte kontext *kubeconfig* pomocí příkazu [az aks get-credentials,][az-aks-get-credentials] který vymaže dříve uložený ověřovací token pro uživatele *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Pokuste se naplánovat a zobrazit pody v přiděleném oboru názvů *sre.* Po zobrazení výzvy se `opssre@contoso.com` přihlaste pomocí vlastních přihlašovacích údajů vytvořených na začátku článku:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Jak je znázorněno v následujícím příkladu výstupu, můžete úspěšně vytvořit a zobrazit pody:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Nyní zkuste zobrazit nebo naplánovat pody mimo přiřazený obor názvů SRE:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Tyto `kubectl` příkazy se nezdaří, jak je znázorněno v následujícím příkladu výstupu. Členství uživatele ve skupině a Kubernetes Role a RoleBindings neudělují oprávnění k vytváření nebo sanaci prostředků v jiných oborech názvů:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jste vytvořili prostředky v clusteru AKS a uživatelé a skupiny ve službě Azure AD. Chcete-li vyčistit všechny tyto prostředky, spusťte následující příkazy:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení clusterů Kubernetes naleznete v [tématu Možnost i možnosti identity pro AKS)][rbac-authorization].

Doporučené postupy týkající se řízení identity a prostředků naleznete [v tématu Doporučené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
