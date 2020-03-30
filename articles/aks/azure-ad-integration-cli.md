---
title: Integrace Azure Active Directory se službou Azure Kubernetes Service
description: Zjistěte, jak pomocí rozhraní příkazového příkazu Azure vytvořit cluster služby Azure Kubernetes (AKS) s podporou Azure Directory a služby Azure Active Directory.
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253049"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrace Azure Active Directory se službou Azure Kubernetes pomocí rozhraní příkazového příkazového příkazu Azure

Azure Kubernetes Service (AKS) lze nakonfigurovat tak, aby používala Azure Active Directory (AD) pro ověřování uživatelů. V této konfiguraci se můžete přihlásit do clusteru AKS pomocí ověřovacího tokenu Azure AD. Operátoři clusteru mohou také konfigurovat řízení přístupu na základě rolí Kubernetes (RBAC) na základě členství uživatele v identitě nebo adresářové skupině.

Tento článek ukazuje, jak vytvořit požadované součásti Azure AD, pak nasadit cluster s podporou Azure AD a vytvořit základní roli RBAC v clusteru AKS. Tyto kroky můžete [také provést pomocí portálu Azure][azure-ad-portal].

Úplný ukázkový skript použitý v tomto článku najdete v [tématu ukázky azure cli – integrace AKS s Azure AD][complete-script].

Platí následující omezení:

- Azure AD lze povolit pouze při vytvoření nového clusteru s podporou RBAC. Azure AD nelze povolit v existujícím clusteru AKS.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

Přejděte [https://shell.azure.com](https://shell.azure.com) na otevření cloudového prostředí ve vašem prohlížeči.

Konzistence a pomoc při spuštění příkazů v tomto článku vytvořte proměnnou pro požadovaný název clusteru AKS. Následující příklad používá název *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Přehled ověřování Azure AD

Ověřování Azure AD se poskytuje clusterům AKS s OpenID Connect. OpenID Connect je vrstva identity postavená na protokolu OAuth 2.0. Další informace o openid connect najdete v [dokumentaci k připojení Open ID][open-id-connect].

Zevnitř clusteru Kubernetes se k ověření ověřovacích tokenů používá ověřování tokenů Webhook. Ověřování tokenů Webhooku je nakonfigurováno a spravováno jako součást clusteru AKS. Další informace o ověřování tokenů Webhooku naleznete v [dokumentaci k ověřování webhooku][kubernetes-webhook].

> [!NOTE]
> Při konfiguraci Azure AD pro ověřování AKS jsou nakonfigurované dvě aplikace Azure AD. Tuto operaci musí dokončit správce klienta Azure.

## <a name="create-azure-ad-server-component"></a>Vytvoření komponenty serveru Azure AD

Chcete-li integrovat s AKS, můžete vytvořit a použít aplikaci Azure AD, která funguje jako koncový bod pro požadavky na identitu. První aplikace Azure AD, kterou potřebujete, získá členství ve skupině Azure AD pro uživatele.

Vytvořte komponentu serverové aplikace pomocí příkazu [vytvořit aplikaci az ad a][az-ad-app-create] pak aktualizujte deklarace členství ve skupině pomocí příkazu [az ad app update.][az-ad-app-update] Následující příklad používá *proměnnou aksname* definovanou v části [Před zahájením](#before-you-begin) a vytvoří proměnnou.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Nyní vytvořte instanční objekt pro serverovou aplikaci pomocí příkazu [az ad sp create.][az-ad-sp-create] Tento instanční objekt služby se používá k ověření sám v rámci platformy Azure. Potom získejte tajný klíč hlavního zabezpečení služby pomocí příkazu [resetování pověření az ad sp][az-ad-sp-credential-reset] a přiřaďte proměnné s názvem *serverApplicationSecret* pro použití v jednom z následujících kroků:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD potřebuje oprávnění k provádění následujících akcí:

* Čtení dat z adresáře
* Přihlášení a čtení profilu uživatele

Přiřaďte tato oprávnění pomocí příkazu [přidat oprávnění k aplikaci az az:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Nakonec udělte oprávnění přiřazená v předchozím kroku pro serverovou aplikaci pomocí příkazu [oprávnění k aplikaci az az.][az-ad-app-permission-grant] Tento krok se nezdaří, pokud aktuální účet není správcem klienta. Je také potřeba přidat oprávnění pro aplikaci Azure AD, abyste mohli požádat o informace, které by jinak mohly vyžadovat souhlas správce pomocí [souhlasu správce az ad app oprávnění][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Vytvoření klientské součásti Azure AD

Druhá aplikace Azure AD se používá, když se uživatel přihlásí do clusteru AKS pomocí rozhraní příkazového příkazu Kubernetes (`kubectl`). Tato klientská aplikace převezme požadavek na ověření od uživatele a ověří jejich pověření a oprávnění. Vytvořte aplikaci Azure AD pro klientskou komponentu pomocí příkazu [vytvořit aplikaci az az:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Vytvořte instanční objekt pro klientskou aplikaci pomocí příkazu [az ad sp create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Získejte ID oAuth2 pro serverovou aplikaci, aby bylo možné povolit tok ověřování mezi dvěma součástmi aplikace pomocí příkazu [az ad app show.][az-ad-app-show] Toto ID oAuth2 se používá v dalším kroku.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Přidejte oprávnění pro součásti klientské aplikace a serverové aplikace pro použití toku komunikace oAuth2 pomocí příkazu [add permission permission permission az app add.][az-ad-app-permission-add] Poté udělte oprávnění klientské aplikaci ke komunikaci se serverovou aplikací pomocí příkazu [az ad app permission grant:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Nasazení clusteru

Se dvěma vytvořenými aplikacemi Azure AD teď vytvořte samotný cluster AKS. Nejprve vytvořte skupinu prostředků pomocí příkazu [az group create.][az-group-create] Následující příklad vytvoří skupinu prostředků v oblasti *EastUS:*

Vytvořte skupinu prostředků pro cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Získejte ID klienta vašeho předplatného Azure pomocí příkazu [az account show.][az-account-show] Potom vytvořte cluster AKS pomocí příkazu [az aks create.][az-aks-create] Příkaz k vytvoření clusteru AKS poskytuje ID serverových a klientských aplikací, tajný klíč hlavního zabezpečení služby aplikace serveru a ID vašeho klienta:

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

Nakonec získejte přihlašovací údaje správce clusteru pomocí příkazu [az aks get-credentials.][az-aks-get-credentials] V jednom z následujících kroků získáte pověření běžného *clusteru uživatelů,* abyste viděli tok ověřování Azure AD v akci.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Vytvořit vazbu RBAC

Před použitím účtu Služby Azure Active Directory s clusterem AKS je třeba vytvořit vazbu role nebo vazbu role clusteru. *Role* definují oprávnění k udělení a *vazby* je aplikují na požadované uživatele. Tato přiřazení lze použít pro daný obor názvů nebo v celém clusteru. Další informace naleznete [v tématu Použití autorizace RBAC][rbac-authorization].

Získejte hlavní jméno uživatele (UPN) uživatele, který je aktuálně přihlášen pomocí příkazu [az az ad signed-in-user show.][az-ad-signed-in-user-show] Tento uživatelský účet je povolen pro integraci Azure AD v dalším kroku.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Pokud uživatel, který udělujete vazbu RBAC pro je ve stejném tenantovi Azure AD, přiřaďte oprávnění na základě *userPrincipalName*. Pokud je uživatel v jiném tenantovi Azure AD, dotaz a místo toho použít *vlastnost objectId.*

Vytvořte manifest YAML s názvem `basic-azure-ad-binding.yaml` a vložte následující obsah. Na posledním řádku nahraďte *userPrincipalName_or_objectId* výstupem HLAVNÍho název nebo ID objektu z předchozího příkazu:

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

Vytvořte clusterrolebinding pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Přístup ke clusteru pomocí Azure AD

Teď otestujeme integraci ověřování Azure AD pro cluster AKS. Nastavte `kubectl` kontext konfigurace tak, aby používal běžná pověření uživatele. Tento kontext předá všechny požadavky na ověření zpět prostřednictvím služby Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Nyní použijte příkaz [kubectl get pods][kubectl-get] k zobrazení podů ve všech oborech názvů:

```console
kubectl get pods --all-namespaces
```

Obdržíte výzvu k přihlášení k ověření pomocí přihlašovacích údajů Azure AD pomocí webového prohlížeče. Po úspěšném ověření `kubectl` příkaz zobrazí pody v clusteru AKS, jak je znázorněno v následujícím příkladu výstupu:

```console
kubectl get pods --all-namespaces
```

```output
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

Ověřovací token přijatý `kubectl` pro je uložen do mezipaměti. K přihlášení se zobrazí pouze v případě, že platnost tokenu vypršela nebo pokud je znovu vytvořen konfigurační soubor Kubernetes.

Pokud se po úspěšném přihlášení pomocí webového prohlížeče zobrazí chybová zpráva autorizace, podívejte se na následující ukázkový výstup, zkontrolujte následující možné problémy:

```output
error: You must be logged in to the server (Unauthorized)
```

* Definovali jste id příslušného objektu nebo hlavní název uživatele, v závislosti na tom, jestli je uživatelský účet ve stejném tenantovi Azure AD nebo ne.
* Uživatel není členem více než 200 skupin.
* Tajný klíč definovaný v registraci aplikace pro server odpovídá hodnotě nakonfigurované pomocí`--aad-server-app-secret`

## <a name="next-steps"></a>Další kroky

Úplný skript, který obsahuje příkazy uvedené v tomto článku, najdete [ve skriptu integrace Azure AD v ukázkách AKS repo][complete-script].

Pokud chcete k řízení přístupu k prostředkům clusteru používat uživatele a skupiny Azure AD k řízení přístupu k prostředkům clusteru, [přečtěte si témat řízení přístupu k prostředkům clusteru pomocí řízení přístupu na základě rolí a identit Azure AD v AKS][azure-ad-rbac].

Další informace o zabezpečení clusterů Kubernetes naleznete v [tématu Možnost i možnosti identity pro AKS)][rbac-authorization].

Doporučené postupy týkající se řízení identity a prostředků naleznete [v tématu Doporučené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
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
