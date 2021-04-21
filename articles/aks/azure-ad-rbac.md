---
title: Použití služby Azure AD a Kubernetes RBAC pro clustery
titleSuffix: Azure Kubernetes Service
description: Naučte se používat členství ve skupině Azure Active Directory k omezení přístupu k prostředkům clusteru pomocí řízení přístupu založeného na rolích (Kubernetes RBAC) ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 0d5171e9e9a5d7f033ff615a3f1205b8dc93966f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769548"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Řízení přístupu k prostředkům clusteru pomocí řízení přístupu na základě role Kubernetes a identit Azure Active Directory ve službě Azure Kubernetes

Službu Azure Kubernetes Service (AKS) je možné nakonfigurovat tak, aby pro ověřování uživatelů používala Azure Active Directory (AD). V této konfiguraci se přihlašujete ke clusteru AKS pomocí ověřovacího tokenu Azure AD. Můžete taky nakonfigurovat Kubernetes řízení přístupu na základě role (Kubernetes RBAC) a omezit tak přístup k prostředkům clusteru, které jsou založené na identitě nebo členství uživatele ve skupině.

Tento článek popisuje, jak pomocí členství ve skupině Azure AD řídit přístup k oborům názvů a prostředkům clusteru pomocí Kubernetes RBAC v clusteru AKS. Ukázkové skupiny a uživatelé se vytvářejí ve službě Azure AD a pak se v clusteru AKS vytvoří role a RoleBindings, které jim udělí příslušná oprávnění k vytváření a zobrazování prostředků.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte povolený existující cluster AKS s integrací služby Azure AD. Pokud potřebujete cluster AKS, přečtěte si téma věnované [integraci Azure Active Directory s AKS][azure-ad-aks-cli].

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Vytvoření ukázkových skupin ve službě Azure AD

V tomto článku vytvoříme dvě role uživatelů, které se dají použít k zobrazení, jak Kubernetes RBAC a Azure AD řídí přístup k prostředkům clusteru. Používají se následující dvě příklady rolí:

* **Vývojář aplikace**
    * Uživatel s názvem *aksdev* , který je součástí skupiny *appdev* .
* **Inženýr spolehlivosti sítě**
    * Uživatel s názvem *akssre* , který je součástí skupiny *opssre* .

V produkčních prostředích můžete použít existující uživatele a skupiny v rámci tenanta Azure AD.

Nejdřív Získejte ID prostředku clusteru AKS pomocí příkazu [AZ AKS show][az-aks-show] . Přiřaďte ID prostředku k proměnné s názvem *AKS_ID* , aby na ně bylo možné odkazovat v dalších příkazech.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Vytvořte první ukázkovou skupinu ve službě Azure AD pro vývojáře aplikací pomocí příkazu [AZ AD Group Create][az-ad-group-create] . Následující příklad vytvoří skupinu s názvem *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Teď vytvořte přiřazení role Azure pro skupinu *appdev* pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] . Toto přiřazení umožňuje všem členům skupiny `kubectl` pracovat s clusterem AKS tím, že jim udělí *roli uživatele clusteru služby Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Pokud se zobrazí chyba `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , třeba, počkejte několik sekund, než se ID objektu skupiny Azure AD rozšíří přes adresář, a potom zkuste `az role assignment create` příkaz zopakovat.

Vytvořte druhou příklad skupiny, který pro SREs s názvem *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Znovu vytvořte přiřazení role Azure a udělte členům skupiny *roli uživatele clusteru služby Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Vytváření ukázkových uživatelů v Azure AD

Pomocí dvou ukázkových skupin vytvořených ve službě Azure AD pro naše vývojáře aplikací a SREs teď umožňuje vytvořit dva ukázkové uživatele. Pokud chcete otestovat integraci Kubernetes RBAC na konci článku, přihlaste se pomocí těchto účtů ke clusteru AKS.

Nastavte hlavní název uživatele (UPN) a heslo pro vývojáře aplikací. Následující příkaz vás vyzve k zadání hlavního názvu uživatele (UPN) a nastaví *AAD_DEV_UPN* pro použití v pozdějším příkazu (Nezapomeňte, že příkazy v tomto článku jsou zadány do prostředí bash). Hlavní název uživatele (UPN) musí zahrnovat ověřený název domény vašeho tenanta, například `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

Následující příkaz vás vyzve k zadání hesla a nastaví *AAD_DEV_PW* pro použití v pozdějším příkazu.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Vytvořte první uživatelský účet ve službě Azure AD pomocí příkazu [AZ AD User Create][az-ad-user-create] .

Následující příklad vytvoří uživatele se zobrazovaným názvem *AKS vývoj* a hlavní název uživatele (UPN) a zabezpečené heslo pomocí hodnot v *AAD_DEV_UPN* a *AAD_DEV_PW*:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Nyní přidejte uživatele do skupiny *appdev* vytvořené v předchozí části pomocí příkazu [AZ AD Group member Add][az-ad-group-member-add] :

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Nastavte hlavní název uživatele (UPN) a heslo pro SREs. Následující příkaz vás vyzve k zadání hlavního názvu uživatele (UPN) a nastaví *AAD_SRE_UPN* pro použití v pozdějším příkazu (Nezapomeňte, že příkazy v tomto článku jsou zadány do prostředí bash). Hlavní název uživatele (UPN) musí zahrnovat ověřený název domény vašeho tenanta, například `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

Následující příkaz vás vyzve k zadání hesla a nastaví *AAD_SRE_PW* pro použití v pozdějším příkazu.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Vytvořte druhý uživatelský účet. Následující příklad vytvoří uživatele se zobrazovaným názvem *AKS SRE* a UPN a zabezpečené heslo pomocí hodnot v *AAD_SRE_UPN* a *AAD_SRE_PW*:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Vytvoření prostředků clusteru AKS pro app vývojáři

Nyní se vytvoří skupiny a uživatelé služby Azure AD. Přiřazení rolí Azure se vytvořilo pro členy skupiny pro připojení ke clusteru AKS jako běžnému uživateli. Teď nakonfigurujeme cluster AKS, aby tyto různé skupiny umožňovaly přístup ke konkrétním prostředkům.

Nejprve pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] Získejte přihlašovací údaje Správce clusteru. V jedné z následujících částí získáte standardní přihlašovací údaje pro *uživatele* clusteru, abyste viděli tok ověřování Azure AD v akci.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Pomocí příkazu [kubectl Create Namespace][kubectl-create] vytvořte v clusteru AKS obor názvů. Následující příklad vytvoří název oboru názvů *dev*:

```console
kubectl create namespace dev
```

V Kubernetes *role* definují oprávnění pro udělení a *RoleBindings* je použijí pro požadované uživatele nebo skupiny. Tato přiřazení lze použít na daný obor názvů nebo v celém clusteru. Další informace najdete v tématu [použití autorizace KUBERNETES RBAC][rbac-authorization].

Nejprve vytvořte roli pro obor názvů pro *vývoj* . Tato role uděluje úplný přístup k oboru názvů. V produkčních prostředích můžete zadat podrobnější oprávnění pro různé uživatele nebo skupiny.

Vytvořte soubor s názvem `role-dev-namespace.yaml` a vložte následující YAML manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
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

Vytvořte roli pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

V dalším kroku Získejte ID prostředku pro skupinu *appdev* pomocí příkazu [AZ AD Group show][az-ad-group-show] . Tato skupina se nastaví jako předmět RoleBinding v dalším kroku.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Nyní vytvořte RoleBinding pro skupinu *appdev* a použijte dříve vytvořenou roli pro přístup k oboru názvů. Vytvořte soubor s názvem `rolebinding-dev-namespace.yaml` a vložte následující manifest YAML. Na posledním řádku nahraďte *groupObjectId*  číslem ID objektu skupiny z předchozího příkazu:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
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

Vytvořte RoleBinding pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Vytvoření prostředků clusteru AKS pro SREs

Teď zopakováním předchozích kroků vytvořte obor názvů, roli a RoleBinding pro SREs.

Nejprve vytvořte obor názvů pro *SRE* pomocí příkazu [kubectl Create Namespace][kubectl-create] :

```console
kubectl create namespace sre
```

Vytvořte soubor s názvem `role-sre-namespace.yaml` a vložte následující YAML manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
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

Vytvořte roli pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

ID prostředku pro skupinu *opssre* získáte pomocí příkazu [AZ AD Group show][az-ad-group-show] :

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Vytvořte RoleBinding pro skupinu *opssre* a použijte dříve vytvořenou roli pro přístup k oboru názvů. Vytvořte soubor s názvem `rolebinding-sre-namespace.yaml` a vložte následující manifest YAML. Na posledním řádku nahraďte *groupObjectId*  číslem ID objektu skupiny z předchozího příkazu:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
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

Vytvořte RoleBinding pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interakce s prostředky clusteru pomocí identit Azure AD

Teď otestujeme, že při vytváření a správě prostředků v clusteru AKS budeme fungovat očekávaná oprávnění. V těchto příkladech můžete naplánovat a zobrazit lusky v oboru názvů přiřazené uživateli. Pak se pokusíte naplánovat a zobrazit lusky mimo přiřazený obor názvů.

Nejdříve obnovte kontext *kubeconfig* pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] . V předchozí části nastavíte kontext pomocí přihlašovacích údajů správce clusteru. Uživatel s rolí správce obchází výzvy přihlášení ke službě Azure AD. Bez `--admin` parametru se použije kontext uživatele, který vyžaduje ověření všech požadavků pomocí Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Naplánujte základní NGINX pod pomocí příkazu [kubectl Run][kubectl-run] v oboru názvů pro *vývoj* :

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Jako přihlašovací výzvu zadejte přihlašovací údaje k vlastnímu `appdev@contoso.com` účtu vytvořené na začátku článku. Po úspěšném přihlášení se token účtu ukládá do mezipaměti pro budoucí `kubectl` příkazy. NGINX je úspěšně naplánován, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Nyní použijte příkaz [kubectl Get lusks][kubectl-get] k zobrazení lusků v oboru názvů pro *vývoj* .

```console
kubectl get pods --namespace dev
```

Jak je znázorněno v následujícím příkladu výstupu, je NGINX pod úspěšně *spuštěn*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Vytváření a zobrazování prostředků clusteru mimo přiřazený obor názvů

Nyní se pokuste zobrazit lusky mimo obor názvů pro *vývoj* . Znovu použijte příkaz [kubectl získat lusky][kubectl-get] , tentokrát se podívejte na `--all-namespaces` následující:

```console
kubectl get pods --all-namespaces
```

Členství ve skupině uživatele nemá roli Kubernetes, která umožňuje tuto akci, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Stejným způsobem se pokuste naplánovat pod v jiném oboru názvů, jako je například obor názvů *SRE* . Členství ve skupině uživatele není zarovnáno s rolí Kubernetes a RoleBinding pro udělení těchto oprávnění, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testování přístupu SRE k prostředkům clusteru AKS

Pokud chcete potvrdit, že naše členství ve skupině Azure AD a Kubernetes RBAC funguje správně mezi různými uživateli a skupinami, zkuste předchozí příkazy přihlášeni jako uživatel *opssre* .

Obnovte kontext *kubeconfig* pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] , který vymaže ověřovací token dříve uložených v mezipaměti pro uživatele *aksdev* :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Zkuste naplánovat a zobrazit lusky v přiřazeném oboru názvů *SRE* . Po zobrazení výzvy se přihlaste s vlastními `opssre@contoso.com` přihlašovacími údaji vytvořenými na začátku článku:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Jak je znázorněno v následujícím příkladu výstupu, můžete úspěšně vytvořit a zobrazit lusky:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Nyní se pokuste zobrazit nebo naplánovat lusky mimo přiřazený obor názvů SRE:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Tyto `kubectl` příkazy selžou, jak je znázorněno v následujícím příkladu výstupu. Členství ve skupinách uživatelů a role Kubernetes a RoleBindings neudělují oprávnění k vytváření prostředků nebo správce v jiných oborech názvů:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jste vytvořili prostředky v clusteru AKS a uživatelích a skupinách v Azure AD. Pro vyčištění všech těchto prostředků spusťte následující příkazy:

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

Další informace o tom, jak zabezpečit clustery Kubernetes, najdete v tématu [Možnosti přístupu a identit pro AKS)][rbac-authorization].

Osvědčené postupy týkající se identit a řízení prostředků najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
