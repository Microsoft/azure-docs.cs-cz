---
title: Integrace Azure Active Directory se službou Azure Kubernetes (starší verze)
description: Naučte se používat Azure CLI k vytvoření a Azure Active Directory clusteru Azure Kubernetes Service (AKS) s povoleným provozem (starší verze).
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 055afe24cb72b331e64e1a2b3d786503ef31a105
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176395"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrace Azure Active Directory se službou Azure Kubernetes pomocí Azure CLI (starší verze)

Službu Azure Kubernetes Service (AKS) je možné nakonfigurovat tak, aby pro ověřování uživatelů používala Azure Active Directory (AD). V této konfiguraci se můžete přihlásit ke clusteru AKS pomocí ověřovacího tokenu Azure AD. Operátoři clusteru můžou také nakonfigurovat řízení přístupu na základě role (Kubernetes RBAC) Kubernetes na základě identity uživatele nebo členství ve skupině adresáře.

V tomto článku se dozvíte, jak vytvořit požadované součásti Azure AD a pak nasadit cluster s podporou Azure AD a vytvořit základní roli Kubernetes v clusteru AKS.

Kompletní vzorový skript použitý v tomto článku najdete v tématu [ukázky v Azure CLI – integrace AKS s Azure AD][complete-script].

> [!Important]
> AKS má nové vylepšené prostředí [Azure AD spravované v AKS][managed-aad] , které nevyžaduje správu serverové nebo klientské aplikace. Pokud chcete provést migraci, postupujte podle pokynů uvedených [tady][managed-aad-migrate].

## <a name="the-following-limitations-apply"></a>Platí následující omezení:

- Azure AD se dá povolit jenom v clusteru Kubernetes s povolenou RBAC.
- Integraci služby Azure AD Legacy lze povolit pouze během vytváření clusteru.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

[https://shell.azure.com](https://shell.azure.com)V prohlížeči otevřete Cloud Shell.

Pro zajištění konzistence a ke spuštění příkazů v tomto článku Vytvořte proměnnou pro požadovaný název clusteru AKS. Následující příklad používá název *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Přehled ověřování Azure AD

Ověřování Azure AD je k dispozici pro clustery AKS s OpenID Connect. OpenID Connect je vrstva identity postavená nad protokolem OAuth 2,0. Další informace o OpenID připojení najdete v dokumentaci k [otevřenému ID Connect][open-id-connect].

V rámci clusteru Kubernetes se ověřování pomocí tokenu Webhooku používá k ověření ověřovacích tokenů. Ověřování tokenu Webhooku je nakonfigurované a spravované jako součást clusteru AKS. Další informace o ověřování tokenů Webhooku najdete v [dokumentaci pro ověřování Webhooku][kubernetes-webhook].

> [!NOTE]
> Při konfiguraci služby Azure AD pro ověřování AKS jsou nakonfigurované dvě aplikace Azure AD. Tuto operaci musí dokončit správce tenanta Azure.

## <a name="create-azure-ad-server-component"></a>Vytvořit součást serveru Azure AD

Pro integraci s AKS vytvoříte a použijete aplikaci Azure AD, která funguje jako koncový bod pro žádosti o identitu. První aplikace Azure AD, kterou potřebujete, získá členství uživatele ve skupině Azure AD.

Vytvořte komponentu serverové aplikace pomocí příkazu [AZ AD App Create][az-ad-app-create] a pak aktualizujte deklarace členství ve skupině pomocí příkazu [AZ AD App Update][az-ad-app-update] . Následující příklad používá proměnnou *aksname* definovanou v oddílu [před začátkem](#before-you-begin) a vytvoří proměnnou.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Nyní vytvořte instanční objekt pro aplikaci serveru pomocí příkazu [AZ AD SP Create][az-ad-sp-create] . Tento instanční objekt se používá k ověření v rámci platformy Azure. Pak pomocí příkazu [AZ AD SP Credential resetu][az-ad-sp-credential-reset] Získejte tajný klíč služby a přiřaďte k proměnné s názvem *serverApplicationSecret* , která se použije v jednom z následujících kroků:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Instanční objekt služby Azure AD potřebuje oprávnění k provedení následujících akcí:

* Čtení dat z adresáře
* Přihlášení a čtení profilu uživatele

Přiřaďte tato oprávnění pomocí příkazu [AZ AD App Permission Add][az-ad-app-permission-add] :

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Nakonec udělte oprávnění přiřazená v předchozím kroku pro serverovou aplikaci pomocí příkazu [AZ AD App oprávnění grant][az-ad-app-permission-grant] . Tento krok se nezdařil, pokud aktuální účet není správcem tenanta. Musíte taky přidat oprávnění pro aplikaci Azure AD, abyste si vyžádali informace, které můžou jinak vyžadovat souhlas správce pomocí žádosti [AZ AD App Permission admin-souhlasu][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Vytvořit součást klienta Azure AD

Druhá aplikace Azure AD se používá, když se uživatel do clusteru AKS přihlásí pomocí rozhraní příkazového řádku Kubernetes ( `kubectl` ). Tato klientská aplikace přijme požadavek na ověření od uživatele a ověří své přihlašovací údaje a oprávnění. Pomocí příkazu [AZ AD App Create][az-ad-app-create] vytvořte aplikaci Azure AD pro komponentu klienta:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Pomocí příkazu [AZ AD SP Create][az-ad-sp-create] vytvořte instanční objekt pro klientskou aplikaci:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Získejte ID oAuth2 pro aplikaci serveru, aby se povolil tok ověřování mezi dvěma komponentami aplikace pomocí příkazu [AZ AD App show][az-ad-app-show] . Toto ID oAuth2 se používá v dalším kroku.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Přidejte oprávnění pro klientské aplikace a součásti serverové aplikace, aby používaly komunikační tok oAuth2 pomocí příkazu [AZ AD App Permission Add][az-ad-app-permission-add] . Pak udělte klientské aplikaci oprávnění ke komunikaci s aplikací serveru pomocí příkazu [AZ AD App oprávnění grant][az-ad-app-permission-grant] :

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Nasazení clusteru

Se dvěma vytvořenými aplikacemi Azure AD teď vytvořte samotný cluster AKS. Nejdřív vytvořte skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] . Následující příklad vytvoří skupinu prostředků v oblasti *EastUS* :

Vytvořte skupinu prostředků pro cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

ID tenanta svého předplatného Azure získáte pomocí příkazu [AZ Account show][az-account-show] . Pak vytvořte cluster AKS pomocí příkazu [AZ AKS Create][az-aks-create] . Příkaz k vytvoření clusteru AKS poskytuje ID aplikací serveru a klienta, tajný klíč hlavní aplikační služby serveru a ID tenanta:

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

Nakonec pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] Získejte přihlašovací údaje Správce clusteru. V jednom z následujících kroků získáte standardní přihlašovací údaje pro *uživatele* clusteru, abyste viděli tok ověřování Azure AD v akci.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Vytvoření vazby RBAC Kubernetes

Předtím, než se dá účet Azure Active Directory použít s clusterem AKS, je potřeba vytvořit vazbu role nebo vazby role clusteru. *Role* definují oprávnění pro udělení a *vazby* je použijí pro požadované uživatele. Tato přiřazení lze použít na daný obor názvů nebo v celém clusteru. Další informace najdete v tématu [použití autorizace KUBERNETES RBAC][rbac-authorization].

Získejte hlavní název uživatele (UPN) pro uživatele, který je aktuálně přihlášený pomocí příkazu [AZ AD Sign-in-User show][az-ad-signed-in-user-show] . Tento uživatelský účet je v dalším kroku povolen pro integraci služby Azure AD.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Pokud je uživateli, kterému udělíte vazbu Kubernetes RBAC, ve stejném tenantovi Azure AD, přiřaďte oprávnění na základě třídy *userPrincipalName*. Pokud je uživatel v jiném tenantovi služby Azure AD, dotaz na a místo toho použijte vlastnost *objectID* .

Vytvořte manifest YAML s názvem `basic-azure-ad-binding.yaml` a vložte následující obsah. Na posledním řádku nahraďte *userPrincipalName_or_objectId*  hlavním názvem uživatele (UPN) nebo ID objektu z předchozího příkazu:

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

Vytvořte ClusterRoleBinding pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název souboru manifestu YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Přístup ke clusteru pomocí Azure AD

Teď otestujeme integraci ověřování Azure AD pro cluster AKS. Nastavte `kubectl` konfigurační kontext tak, aby používal běžné přihlašovací údaje uživatele. Tento kontext projde všechny žádosti o ověření zpět prostřednictvím služby Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Nyní použijte příkaz [kubectl Get lusks][kubectl-get] k zobrazení lusků napříč všemi obory názvů:

```console
kubectl get pods --all-namespaces
```

Zobrazí se výzva pro přihlášení k ověření pomocí přihlašovacích údajů Azure AD pomocí webového prohlížeče. Po úspěšném ověření se v `kubectl` příkazu zobrazí lusky v clusteru AKS, jak je znázorněno v následujícím příkladu výstupu:

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

Ověřovací token přijatý pro `kubectl` je uložený v mezipaměti. Znovu se zobrazí výzva, abyste se přihlásili, když vypršela platnost tokenu, nebo se konfigurační soubor Kubernetes znovu vytvoří.

Pokud se zobrazí chybová zpráva autorizace po úspěšném přihlášení pomocí webového prohlížeče jako v následujícím příkladu výstupu, podívejte se na následující možné problémy:

```output
error: You must be logged in to the server (Unauthorized)
```

* V závislosti na tom, jestli je uživatelský účet ve stejném tenantovi Azure AD, jste definovali příslušné ID objektu nebo hlavní název uživatele (UPN).
* Uživatel není členem více než 200 skupin.
* Tajný kód definovaný v registraci aplikace pro server odpovídá hodnotě nakonfigurované pomocí `--aad-server-app-secret`

## <a name="next-steps"></a>Další kroky

Úplný skript, který obsahuje příkazy uvedené v tomto článku, najdete v tématu věnovaném [skriptu integrace služby Azure AD v úložišti UKÁZEK AKS][complete-script].

Postup použití uživatelů a skupin Azure AD k řízení přístupu k prostředkům clusteru najdete v tématu [řízení přístupu k prostředkům clusteru pomocí řízení přístupu na základě role Kubernetes a identit Azure AD v AKS][azure-ad-rbac].

Další informace o tom, jak zabezpečit clustery Kubernetes, najdete v tématu [Možnosti přístupu a identit pro AKS)][rbac-authorization].

Osvědčené postupy týkající se identit a řízení prostředků najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
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
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
