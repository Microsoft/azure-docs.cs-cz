---
title: Integrace služby Azure Active Directory s Azure Kubernetes Service
description: Zjistěte, jak pomocí Azure CLI k vytvoření a clusteru s podporou Azure Active Directory Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: 86b9609d5141798be40f53aab8b18897484bbef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467574"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrace Azure Active Directory s Azure Kubernetes Service pomocí Azure CLI

Azure Kubernetes Service (AKS) je nakonfigurovat pro ověřování uživatelů pomocí Azure Active Directory (AD). V této konfiguraci se můžete přihlásit cluster AKS pomocí tokenu ověřování Azure AD. Operátory clusteru můžete také nakonfigurovat Kubernetes řízení přístupu na základě rolí (RBAC) na základě členství ve skupině uživatelské identity nebo adresáře.

V tomto článku se dozvíte, jak vytvořit požadované součásti služby Azure AD, pak Nasaďte cluster Azure AD povolené a vytvoření základní role RBAC v clusteru AKS. Můžete také [proveďte následující kroky pomocí webu Azure portal][azure-ad-portal].

Platí následující omezení:

- Azure AD jde Povolit jenom při vytváření nové, RBAC s podporou clusteru. Nejde povolit Azure AD v existujícím clusteru AKS.
- *Host* uživatelů ve službě Azure AD, například jako v případě, že používáte federované přihlášení z jiného adresáře, nejsou podporovány.

## <a name="before-you-begin"></a>Než začnete

Musí mít Azure CLI verze 2.0.61 nebo později nainstalována a nakonfigurována. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

Pro zajištění konzistence a ke spuštění příkazů v tomto článku vytvořte proměnnou pro požadovaný název clusteru AKS. Následující příklad používá název *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Přehled ověřování Azure AD

Ověřování Azure AD je k dispozici do AKS clusterů s OpenID Connect. OpenID Connect se vrstvu identit postavené na protokol OAuth 2.0. Další informace o OpenID Connect, najdete v článku [Open ID connect dokumentaci][open-id-connect].

Z v rámci clusteru Kubernetes, ověřování pomocí tokenu Webhooku slouží k ověření ověřovacích tokenů. Ověřování pomocí tokenu Webhooku je nakonfigurovat a spravovat jako součást clusteru AKS. Další informace o ověřování pomocí tokenu Webhooku, najdete v článku [dokumentace ověřování webhooku][kubernetes-webhook].

> [!NOTE]
> Při konfiguraci Azure AD pro ověřování AKS, jsou nakonfigurovány dvě aplikace Azure AD. Tuto operaci musíte dokončit microsoftem nebo správcem tenanta Azure.

## <a name="create-azure-ad-server-component"></a>Vytvořit součást serveru Azure AD

Integrace se službou AKS, vytvořit a používat aplikaci Azure AD, který funguje jako koncový bod pro požadavky na identity. První aplikaci Azure AD, které potřebujete získá členství ve skupině Azure AD pro uživatele.

Vytvoření s použitím komponenty aplikace serveru [az ad app vytvořit] [ az-ad-app-create] příkaz a pak aktualizace členství ve skupině deklarací identity pomocí [az ad app update] [ az-ad-app-update] příkazu. V následujícím příkladu *aksname* proměnná definovaná v [před zahájením](#before-you-begin) části a vytvoří proměnnou

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Nyní vytvořit instanční objekt pro aplikaci pomocí serveru [az ad sp vytvořit] [ az-ad-sp-create] příkazu. Tento instanční objekt se používá ke svému ověření v rámci platformy Azure. Potom získejte instanční objekt tajného kódu s použitím [přihlašovacích údajů az ad sp reset] [ az-ad-sp-credential-reset] příkazů a přiřaďte jej do proměnné s názvem *serverApplicationSecret* pro použití v jednom z Následující kroky:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Služba Azure AD potřebuje oprávnění k provedení následujících akcí:

* Čtení dat adresáře
* Přihlášení a čtení profilu uživatele
* Čtení dat adresáře

Toto přiřazení oprávnění pomocí [az ad app oprávnění Přidat] [ az-ad-app-permission-add] příkaz:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Nakonec udělit oprávnění přiřazené v předchozím kroku pro server pomocí aplikace [az ad app oprávnění udělit] [ az-ad-app-permission-grant] příkazu. Tento krok nezdaří, pokud není aktuální účet správce tenanta. Budete také muset přidat oprávnění aplikace Azure AD pro žádost o informace, které jinak může vyžadovat souhlas správce pomocí [az ad app oprávnění-souhlas správce][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Vytvořit součást klienta Azure AD

Druhá aplikace Azure AD se používá, když se uživatel přihlásí do clusteru AKS pomocí rozhraní příkazového řádku Kubernetes (`kubectl`). Tato klientská aplikace přijímá žádosti o ověření od uživatele a ověří jejich účtech a oprávněních. Vytvoření aplikace Azure AD pro používání součásti klienta [az ad app vytvořit] [ az-ad-app-create] příkaz:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Vytvoření instančního objektu pro klientská aplikace používající [az ad sp vytvořit] [ az-ad-sp-create] příkaz:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Získejte ID oAuth2 pro serverovou aplikaci povolit tok ověřování mezi součástmi dvou aplikací pomocí [az ad app show] [ az-ad-app-show] příkazu. Toto ID oAuth2 se používá v dalším kroku.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Přidání oprávnění pro klientské aplikace a součásti serveru aplikace pro použití komunikace oAuth2 tok pomocí [az ad app oprávnění Přidat] [ az-ad-app-permission-add] příkazu. Udělte oprávnění pro klientskou aplikaci pro komunikaci se serverem aplikace pomocí [az ad app oprávnění udělit] [ az-ad-app-permission-grant] příkaz:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Nasazení clusteru

S dvě aplikace Azure AD vytvoří nyní vytvořte samotného clusteru AKS. Nejprve vytvořte skupinu prostředků pomocí [vytvořit skupiny az] [ az-group-create] příkazu. Následující příklad vytvoří skupinu prostředků *EastUS* oblasti:

Vytvořte skupinu prostředků pro cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Získání ID tenanta pomocí předplatného Azure [zobrazit účet az] [ az-account-show] příkazu. Potom vytvořte cluster AKS pomocí [az aks vytvořit] [ az-aks-create] příkazu. Příkaz pro vytvoření clusteru AKS poskytuje serveru a klienta, ID aplikace, server služby hlavní tajný klíč aplikace a ID vašeho tenanta:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Nakonec získejte clusteru pomocí přihlašovacích údajů správce [az aks get-credentials] [ az-aks-get-credentials] příkazu. V jednom z následujících kroků získejte standardní *uživatele* přihlašovacích údajů pro ověřování Azure AD najdete v clusteru toku v činnosti.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Vytvoření vazby RBAC

Předtím, než účet služby Azure Active Directory je možné s clusterem AKS, role vazby nebo vazby role clusteru je potřeba vytvořit. *Role* definovat oprávnění udělit, a *vazby* platí pro požadovaného uživatele. Tato přiřazení můžete použít k daném oboru názvů nebo přes celý cluster. Další informace najdete v tématu [pomocí RBAC se podařilo Autorizovat][rbac-authorization].

Získat hlavní název uživatele (UPN) pro uživatel momentálně přihlášen pomocí [az ad podepsán v uživatele zobrazit] [ az-ad-signed-in-user-show] příkazu. Tento uživatelský účet je povolena pro integraci služby Azure AD v dalším kroku.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Pokud je uživatel udělit vazby RBAC pro ve stejném tenantovi Azure AD, přiřaďte oprávnění podle principu *userPrincipalName*. Pokud se uživatel nachází v jiné službě Azure AD tenanta, dotázat a použít *objectId* vlastnost místo.

Vytvoření YAML manifestu s názvem `basic-azure-ad-binding.yaml` a vložte následující obsah. Na posledním řádku, nahraďte *userPrincipalName_or_objectId* s výstupem ID (UPN) nebo objekt z předchozího příkazu:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Vytvoření s použitím ClusterRoleBinding [použití kubectl] [ kubectl-apply] příkaz a zadejte název souboru manifestu YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Přístup k clusteru se službou Azure AD

Teď můžeme otestovat integraci ověřování Azure AD pro AKS cluster. Nastavte `kubectl` kontextu konfigurace pomocí běžných uživatelských přihlašovacích údajů. Tento kontext projde všechny požadavky na ověření zpět prostřednictvím služby Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Teď použijte [kubectl get pods] [ kubectl-get] příkazu zobrazíte podů přes všechny obory názvů:

```console
kubectl get pods --all-namespaces
```

Přihlášení se zobrazí v výzva k ověření pomocí přihlašovacích údajů Azure AD pomocí webového prohlížeče. Po úspěšném ověření, `kubectl` příkaz zobrazí podů v clusteru AKS, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Ověřovací token byl přijat pro `kubectl` se uloží do mezipaměti. Jsou pouze získat k přihlášení během tokenu vypršela nebo je znovu vytvořit konfigurační soubor Kubernetes.

Pokud se zobrazí chybovou zprávu ověření poté, co jste úspěšně přihlášení pomocí webového prohlížeče jako následující příklad výstupu, zkontrolujte následující možné problémy:

```console
error: You must be logged in to the server (Unauthorized)
```

* Uživatel se v, protože není přihlášený *hosta* v instanci Azure AD (to je často případ používáte federované přihlašování z jiného adresáře).
* Uživatel není členem více než 200 skupin.

## <a name="next-steps"></a>Další postup

Celý skript, který obsahuje příkazy uvedené v tomto článku, najdete v článku [skript pro integraci služby Azure AD v AKS ukázky úložiště][complete-script].

Řízení přístupu k prostředkům clusteru pomocí Azure AD Uživatelé a skupiny, najdete v článku [řízení přístupu k prostředkům clusteru pomocí Azure AD identity a řízení přístupu na základě role ve službě AKS][azure-ad-rbac].

Další informace o tom, jak zabezpečit clustery Kubernetes najdete v tématu [možnosti přístupu a identit pro AKS)][rbac-authorization].

Osvědčené postupy na řízení prostředků a identit, naleznete v tématu [osvědčené postupy pro ověřování a autorizace ve službě AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
