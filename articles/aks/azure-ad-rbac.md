---
title: Ovládací prvek prostředků clusteru pomocí RBAC a Azure AD ve službě Azure Kubernetes
description: Další informace o použití členství ve skupině Azure Active Directory k omezení přístupu k prostředkům clusteru pomocí řízení přístupu na základě role (RBAC) ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467540"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Řízení přístupu k prostředkům clusteru pomocí Azure Active Directory identity a řízení přístupu na základě role ve službě Azure Kubernetes

Azure Kubernetes Service (AKS) je nakonfigurovat pro ověřování uživatelů pomocí Azure Active Directory (AD). V této konfiguraci se přihlásíte k cluster AKS pomocí tokenu ověřování Azure AD. Můžete také nakonfigurovat Kubernetes řízení přístupu na základě rolí (RBAC) k omezení přístupu k prostředkům clusteru na základě členství uživatele identitu nebo skupinu.

Tento článek ukazuje, jak použít členství ve skupině Azure AD a řízení přístupu na obory názvů pomocí Kubernetes RBAC v clusteru AKS prostředky clusteru. Příklad skupin a uživatelů se vytvoří ve službě Azure AD a role a RoleBindings se vytvoří v clusteru AKS udělit příslušná oprávnění k vytváření a zobrazování prostředků.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte existující cluster AKS povolená díky integraci služby Azure AD. Pokud potřebujete AKS cluster, přečtěte si [integrace Azure Active Directory s AKS][azure-ad-aks-cli].

Musí mít Azure CLI verze 2.0.61 nebo později nainstalována a nakonfigurována. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Vytvoření ukázky skupiny ve službě Azure AD

V tomto článku vytvoříte dvě role uživatelů, které lze použít k zobrazení, jak Kubernetes RBAC a Azure AD řízení přístupu k prostředkům clusteru. Používají se tyto dva vzorové role:

* **Vývojář aplikace**
    * Uživatel se jménem *aksdev* , který je součástí *appdev* skupiny.
* **Inženýr webové spolehlivosti**
    * Uživatel se jménem *akssre* , který je součástí *opssre* skupiny.

V produkčním prostředí můžete použít existující uživatele a skupiny v rámci tenanta služby Azure AD.

Nejprve Získejte ID prostředku clusteru AKS pomocí [az aks zobrazit] [ az-aks-show] příkazu. ID prostředku přiřadit proměnné s názvem *AKS_ID* tak, aby jej lze odkazovat v dalších příkazů.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Vytvoření první příklad skupiny ve službě Azure AD pro vývojáře aplikací pomocí [vytvořit skupiny ad az] [ az-ad-group-create] příkazu. Následující příklad vytvoří skupinu *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Teď vytvořte přiřazení Azure role pro *appdev* skupinou pomocí [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu. Toto přiřazení umožňuje všechny členy skupiny použijte `kubectl` k interakci s clusterem AKS tak, že jim udělíte *Role uživatele pro Cluster Azure Kubernetes Service*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Pokud se třeba zobrazí chyba `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, počkejte několik sekund pro ID objektu skupiny Azure AD k rozšíří v rámci adresáře a zkuste `az role assignment create` příkaz znovu.

Vytvořte skupinu druhý příklad, s názvem pro SREs *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Přiřazení Azure role pro udělení členy skupiny znovu vytvořit *Role uživatele pro Cluster Azure Kubernetes Service*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Vytvoření ukázkové uživatelů ve službě Azure AD

Se dvěma skupinami příklad vytvoří ve službě Azure AD pro naši vývojáři aplikací a SREs Pojďme vytvořit dva příklady uživatelů. Testování integrace RBAC na konci tohoto článku, přihlášení ke clusteru AKS pomocí těchto účtů.

Vytvořit první účet uživatele v Azure AD používat [vytvořit uživatele ad az] [ az-ad-user-create] příkazu.

Následující příklad vytvoří uživatele se zobrazovaným názvem *AKS Dev* a hlavní název uživatele (UPN) z `aksdev@contoso.com`. Aktualizovat hlavní název uživatele chcete zahrnout ověřenou doménou pro vašeho tenanta Azure AD (nahradit *contoso.com* s vlastní doménou) a zadejte vlastní zabezpečené `--password` přihlašovacích údajů:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Nyní přidejte uživatele do *appdev* skupinu vytvořenou v předchozím oddílu pomocí [přidat člena skupiny ad az] [ az-ad-group-member-add] příkaz:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Vytvořte druhý uživatelský účet. Následující příklad vytvoří uživatele se zobrazovaným názvem *AKS SRE* a hlavní název uživatele (UPN) z `akssre@contoso.com`. Znovu, aktualizujte hlavní název uživatele chcete zahrnout ověřenou doménou pro vašeho tenanta Azure AD (nahradit *contoso.com* s vlastní doménou) a zadejte vlastní zabezpečené `--password` přihlašovacích údajů:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Vytvořit prostředky clusteru AKS pro vývojáře aplikací

Teď se vytvoří skupiny Azure AD a uživatelů. Přiřazení Azure role byly vytvořeny pro členy skupiny pro připojení ke clusteru AKS jako běžný uživatel. Nyní nakonfigurujeme clusteru AKS, pokud chcete povolit tyto různé skupiny přístup ke konkrétním prostředkům.

Nejprve získejte clusteru pomocí přihlašovacích údajů správce [az aks get-credentials] [ az-aks-get-credentials] příkazu. Jedním z následujících částí, obdržíte standardní *uživatele* přihlašovacích údajů pro ověřování Azure AD najdete v clusteru toku v činnosti.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Vytvoření oboru názvů v clusteru AKS pomocí [kubectl vytvoření oboru názvů] [ kubectl-create] příkazu. Následující příklad vytvoří název oboru názvů *dev*:

```console
kubectl create namespace dev
```

V systému Kubernetes *role* definovat oprávnění udělit, a *RoleBindings* aplikovat požadované uživatelům nebo skupinám. Tato přiřazení můžete použít k daném oboru názvů nebo přes celý cluster. Další informace najdete v tématu [pomocí RBAC se podařilo Autorizovat][rbac-authorization].

Nejprve vytvořte roli pro *dev* oboru názvů. Tato role uděluje úplná oprávnění k oboru názvů. V produkčním prostředí můžete určit podrobnější oprávnění pro různé uživatele nebo skupiny.

Vytvořte soubor s názvem `role-dev-namespace.yaml` a vložte následující YAML manifestu:

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

Vytvoření s použitím Role [použití kubectl] [ kubectl-apply] příkaz a zadejte název souboru manifestu YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Potom Získejte ID prostředku pro *appdev* skupinou pomocí [az ad skupiny zobrazit] [ az-ad-group-show] příkazu. Tato skupina je nastavena jako předmět RoleBinding v dalším kroku.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Teď vytvořte RoleBinding pro *appdev* skupiny použijte dříve vytvořenou roli pro přístup k oboru názvů. Vytvořte soubor s názvem `rolebinding-dev-namespace.yaml` a vložte následující YAML manifestu. Na posledním řádku, nahraďte *groupObjectId* s výstupem ID objektu skupiny z předchozího příkazu:

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

Vytvoření s použitím RoleBinding [použití kubectl] [ kubectl-apply] příkaz a zadejte název souboru manifestu YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Vytvořit prostředky clusteru AKS pro SREs

Nyní opakujte předchozí kroky k vytvoření oboru názvů, Role a RoleBinding pro SREs.

Nejprve vytvořte obor názvů pro *sre* pomocí [kubectl vytvoření oboru názvů] [ kubectl-create] příkaz:

```console
kubectl create namespace sre
```

Vytvořte soubor s názvem `role-sre-namespace.yaml` a vložte následující YAML manifestu:

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

Vytvoření s použitím Role [použití kubectl] [ kubectl-apply] příkaz a zadejte název souboru manifestu YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Získejte ID prostředku pro *opssre* skupinou pomocí [az ad skupiny zobrazit] [ az-ad-group-show] příkaz:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Vytvoření RoleBinding pro *opssre* skupiny použijte dříve vytvořenou roli pro přístup k oboru názvů. Vytvořte soubor s názvem `rolebinding-sre-namespace.yaml` a vložte následující YAML manifestu. Na posledním řádku, nahraďte *groupObjectId* s výstupem ID objektu skupiny z předchozího příkazu:

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

Vytvoření s použitím RoleBinding [použití kubectl] [ kubectl-apply] příkaz a zadejte název souboru manifestu YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interakce s prostředky clusteru pomocí identit Azure AD

Teď můžeme otestovat očekávaná oprávnění práce při vytváření a správě prostředků v clusteru AKS. V těchto příkladech naplánovat a zobrazit podů v oboru názvů přiřazené uživatele. Pak zkuste plán a zobrazení podů mimo přiřazenou oboru názvů.

Nejdříve obnovit *kubeconfig* pomocí kontextu [az aks get-credentials] [ az-aks-get-credentials] příkazu. V předchozí části můžete nastavit kontext pomocí přihlašovacích údajů Správce clusteru. Uživatel s rolí správce obchází výzev k přihlášení Azure AD. Bez `--admin` parametr, použije se kontext uživatele se použije, která vyžaduje všechny žádosti o ověření pomocí služby Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Plán basic pod NGINX pomocí [kubectl spustit] [ kubectl-run] v příkaz *dev* obor názvů:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Jako znak na řádku, zadejte přihlašovací údaje pro vlastní `appdev@contoso.com` účet vytvořili na začátku tohoto článku. Jakmile jste úspěšně přihlášení, je token účtu uložená v mezipaměti pro budoucí `kubectl` příkazy. Serveru NGINX se úspěšně naplánovat, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Teď použijte [kubectl get pods] [ kubectl-get] příkazu zobrazíte podů v *dev* oboru názvů.

```console
kubectl get pods --namespace dev
```

Jak ukazuje následující příklad výstupu, NGINX pod se úspěšně *systémem*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Vytvořit a zobrazit prostředky clusteru mimo přiřazenou oboru názvů

Nyní se pokusí zobrazit podů mimo *dev* oboru názvů. Použití [kubectl get pods] [ kubectl-get] příkaz znovu, tentokrát zobrazíte `--all-namespaces` následujícím způsobem:

```console
kubectl get pods --all-namespaces
```

Členství ve skupinách daného uživatele nemá roli Kubernetes, který umožňuje tím, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Stejným způsobem, zkuste naplánovat pod v jiný obor názvů, jako *sre* oboru názvů. Členství ve skupinách daného uživatele není sladěný s Kubernetes Role a RoleBinding udělení oprávnění, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testování SRE přístupu k prostředkům clusteru AKS

Pokud chcete potvrdit, že naše členství ve skupině Azure AD a Kubernetes RBAC fungovat správně mezi různými uživateli a skupinami, zkuste předchozích příkazů při přihlášení jako *opssre* uživatele.

Obnovit *kubeconfig* pomocí kontextu [az aks get-credentials] [ az-aks-get-credentials] příkaz, který vymaže uložené v mezipaměti ověřovací token pro *aksdev*  uživatele:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Zkuste naplánovat a zobrazit podů v přiřazená *sre* oboru názvů. Po zobrazení výzvy, přihlaste se pomocí vlastní `opssre@contoso.com` přihlašovacích údajů na začátku tohoto článku vytvořili:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Jak ukazuje následující příklad výstupu, můžete úspěšně vytvořit a zobrazit tyto pody:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Teď si vyzkoušejte zobrazit nebo naplánovat podů mimo přiřazené SRE obor názvů:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Tyto `kubectl` příkazy selžou, jak je znázorněno v následujícím příkladu výstupu. Členství ve skupině a Kubernetes Role a RoleBindings uživatele není udělit oprávnění k vytvoření nebo správce prostředků v jiných oborech názvů:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jste vytvořili prostředky v clusteru AKS a uživatele a skupiny ve službě Azure AD. K uvolnění těchto prostředků, spusťte následující příkazy:

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

## <a name="next-steps"></a>Další postup

Další informace o tom, jak zabezpečit clustery Kubernetes najdete v tématu [možnosti přístupu a identit pro AKS)][rbac-authorization].

Osvědčené postupy na řízení prostředků a identit, naleznete v tématu [osvědčené postupy pro ověřování a autorizace ve službě AKS][operator-best-practices-identity].

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
