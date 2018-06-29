---
title: Integrovat Azure Kubernetes služby Azure Active Directory
description: Jak vytvořit clustery s podporou služby Azure Active Directory Azure Kubernetes Service.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 272d98613e13c1bb76c75befd6bd5e0115c32610
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097236"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Integrovat AKS - Preview služby Azure Active Directory

Služba Azure Kubernetes (AKS) lze nakonfigurovat k využívání služby Azure Active Directory pro ověřování uživatelů. V této konfiguraci může přihlásit k clusteru Azure Kubernetes Service pomocí tokenu ověřování Azure Active Directory. Kromě toho Správce clusterů mohou konfigurace řízení přístupu na základě rolí Kubernetes podle uživatelů identity nebo adresář členství ve skupině.

Tento dokument podrobně popisuje vytvoření všech nezbytných předpokladů pro AKS a Azure AD, nasazení služby Azure AD s podporou clusteru a vytvoření jednoduché role RBAC v AKS clusteru.

> [!IMPORTANT]
> Integrace Azure RBAC služby Kubernetes (AKS) a Azure AD je aktuálně v **preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.
>

## <a name="authentication-details"></a>Podrobnosti o ověřování

Ověřování služby Azure AD je určen k Azure Kubernetes clusterů s OpenID Connect. OpenID Connect je vrstvu identit postavená na protokol OAuth 2.0. Další informace o OpenID Connect naleznete v [Open ID connect dokumentaci][open-id-connect].

Z uvnitř clusteru Kubernetes Webhooku tokenu ověřování používají k ověření tokeny ověřování. Ověření pomocí tokenu Webhooku je nakonfigurované a spravované v rámci clusteru AKS. Další informace o Webhooku tokenu ověřování najdete v [webhooku ověřování dokumentaci][kubernetes-webhook].

> [!NOTE]
> Při konfiguraci Azure AD pro ověřování AKS, jsou nakonfigurované dva aplikaci Azure AD. Tuto operaci musíte dokončit správce klientovi Azure.

## <a name="create-server-application"></a>Vytvoření aplikace serveru

První aplikace Azure AD se používá k získání členství ve skupině uživatelů Azure AD.

1. Vyberte položky **Azure Active Directory** > **Registrace aplikací** > **Registrace nové aplikace**.

  Pojmenujte aplikaci, vyberte **webovou aplikaci nebo API** pro typ aplikace a zadejte libovolnou hodnotu formátu identifikátoru URI pro **přihlašovací adresa URL**. Vyberte **vytvořit** po dokončení.

  ![Vytvoření registrace Azure AD](media/aad-integration/app-registration.png)

2. Vyberte **Manifest** a upravit `groupMembershipClaims` hodnotu `"All"`.

  Aktualizace po dokončení uložte.

  ![Aktualizovat členství ve skupině pro všechny](media/aad-integration/edit-manifest.png)

3. Zpět na aplikace Azure AD, vyberte **nastavení** > **klíče**.

  Přidejte klíče popis, vyberte termín vypršení platnosti a vyberte **Uložit**. Poznamenejte si hodnotu klíče. Při nasazení Azure AD povolili AKS clusteru, tato hodnota se označuje jako `Server application secret`.

  ![Získat soukromý klíč aplikace](media/aad-integration/application-key.png)

4. Vrátit se do aplikace Azure AD, vyberte **nastavení** > **požadovaná oprávnění** > **přidat**  >   **Vybrat rozhraní API** > **Microsoft Graph** > **vyberte**.

  V části **oprávnění aplikací** zaškrtněte vedle **čtení dat adresáře**.

  ![Nastavte oprávnění aplikací grafu](media/aad-integration/read-directory.png)

5. V části **DELEGOVANÁ oprávnění**, zaškrtněte vedle **přihlášení a čtení profilu uživatele** a **čtení dat adresáře**. Uložte aktualizace jednou provést.

  ![Nastavte oprávnění aplikací grafu](media/aad-integration/delegated-permissions.png)

6. Vyberte **provádí** a **udělit oprávnění** k dokončení tohoto kroku. Tento krok se nezdaří, pokud není aktuální účet správce klienta

  ![Nastavte oprávnění aplikací grafu](media/aad-integration/grant-permissions.png)

7. Vraťte se k aplikaci a poznamenejte si **ID aplikace**. Při nasazení clusteru služby Azure AD s podporou AKS, tato hodnota se označuje jako `Server application ID`.

  ![Získání ID aplikace](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Vytvoření aplikace klienta

Druhý aplikace Azure AD se používá při přihlašování pomocí rozhraní příkazového řádku Kubernetes (kubectl.)

1. Vyberte položky **Azure Active Directory** > **Registrace aplikací** > **Registrace nové aplikace**.

  Zadejte název, vyberte aplikaci **nativní** pro typ aplikace a zadejte libovolnou hodnotu formátu identifikátoru URI pro **identifikátor URI pro přesměrování**. Vyberte **vytvořit** po dokončení.

  ![Vytvoření registrace AAD](media/aad-integration/app-registration-client.png)

2. Aplikace Azure AD, vyberte **nastavení** > **požadovaná oprávnění** > **přidat** > **vyberte Rozhraní API** a vyhledávání pro název serveru aplikace vytvořené v posledním kroku tohoto dokumentu.

  ![Konfigurace oprávnění aplikací](media/aad-integration/select-api.png)

3. Zaškrtněte políčko vedle aplikace a klikněte na tlačítko **vyberte**.

  ![Vyberte AKS AAD koncový bod serveru aplikace](media/aad-integration/select-server-app.png)

4. Vyberte **provádí** a **udělit oprávnění** k dokončení tohoto kroku.

  ![Udělení oprávnění](media/aad-integration/grant-permissions-client.png)

5. Zpět na aplikaci AD, poznamenejte si **ID aplikace**. Při nasazení clusteru služby Azure AD s podporou AKS, tato hodnota se označuje jako `Client application ID`.

  ![Získání ID aplikace](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Získání ID tenanta

Nakonec se získáte ID klienta služby Azure. Tato hodnota se také používá při nasazování clusteru AKS.

Z portálu Azure vyberte **Azure Active Directory** > **vlastnosti** a poznamenejte si **ID adresáře**. Při nasazení clusteru služby Azure AD s podporou AKS, tato hodnota se označuje jako `Tenant ID`.

![Získání ID klienta Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Nasazení clusteru

Použití [vytvořit skupinu az] [ az-group-create] příkazu vytvořte skupinu prostředků clusteru AKS.

```azurecli
az group create --name myAKSCluster --location eastus
```

Nasazení clusteru pomocí [vytvořit az aks] [ az-aks-create] příkaz. Nahraďte hodnoty v příkazu ukázka níže hodnoty shromažďované při vytváření aplikace Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Vytvoření vazby RBAC

Před použitím účet služby Azure Active Directory s clusterem AKS vazby role nebo vazby role clusteru musí být vytvořen.

Nejprve pomocí [az aks get pověření] [ az-aks-get-credentials] s `--admin` argument pro přihlášení do clusteru s přístupem správce.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

Pak pomocí následujících manifest vytvořit ClusterRoleBinding pro účet služby Azure AD. Aktualizace uživatelské jméno s jedním z vašeho klienta Azure AD. Tento příklad poskytuje úplný přístup účtu na všechny obory názvů clusteru.

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
  name: "user@contoso.com"
```

Můžete také vytvořit vazbu role pro všechny členy skupiny služby Azure AD. Následující manifest poskytuje všechny členy `kubernetes-admin` skupiny přístup správce ke clusteru.

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
   kind: Group
   name: "kubernetes-admin"
```

Další informace o zabezpečení clusteru Kubernetes s RBAC najdete v tématu [autorizace pomocí RBAC][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Cluster přístup s Azure AD

V dalším kroku pro vyžádání obsahu kontext pro uživatele bez oprávnění správce s využitím [az aks get pověření] [ az-aks-get-credentials] příkaz.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Po spuštění příkazu žádné kubectl, zobrazí se výzva k ověření pomocí Azure. Postupujte podle na obrazovce pokyny.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Po dokončení se uloží do mezipaměti ověřovací token. Jsou pouze získat přihlásit Pokud vypršela platnost tokenu nebo konfiguračního souboru Kubernetes znovu vytvořit.

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení Kubernetes clusterů s RBAC s [autorizace pomocí RBAC] [ rbac-authorization] dokumentaci.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
