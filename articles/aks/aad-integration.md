---
title: Integrace služby Azure Active Directory s Azure Kubernetes Service
description: Jak vytvářet clustery s podporou Azure Active Directory Azure Kubernetes Service.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7ae3818795cddf5dfbb93ca6cc8dfff9d1c44c03
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341230"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>Integrace Azure Active Directory s AKS – Preview

Azure Kubernetes Service (AKS) je nakonfigurovat pro použití služby Azure Active Directory k ověřování uživatelů. V této konfiguraci se můžete přihlásit cluster Azure Kubernetes Service pomocí tokenu ověřování Azure Active Directory. Kromě toho Správce clusterů budou moct konfigurovat řízení přístupu na základě rolí Kubernetes podle uživatelů identity nebo adresář členství ve skupině.

Tento dokument podrobně popisuje vytvoření všechny nezbytné požadavky pro Azure AD a AKS, nasazení clusteru Azure AD povolené a vytvoření jednoduchého role RBAC v clusteru AKS.

> [!IMPORTANT]
> Integrace služby Azure Kubernetes Service (AKS) RBAC a Azure AD je aktuálně ve **ve verzi preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.
>

## <a name="authentication-details"></a>Podrobnosti o ověřování

Ověřování Azure AD je k dispozici ke clusterům Kubernetes v Azure s OpenID Connect. OpenID Connect se vrstvu identit postavené na protokol OAuth 2.0. Další informace o OpenID Connect najdete v [Open ID connect dokumentaci][open-id-connect].

Z v rámci clusteru Kubernetes, ověřování pomocí tokenu Webhooku slouží k ověření ověřovacích tokenů. Ověřování pomocí tokenu Webhooku je nakonfigurovat a spravovat jako součást clusteru AKS. Další informace o Webhooků, ověřování pomocí tokenu lze nalézt v [dokumentace ověřování webhooku][kubernetes-webhook].

> [!NOTE]
> Při konfiguraci Azure AD pro ověřování AKS, jsou nakonfigurovány dvě aplikace Azure AD. Tuto operaci musíte dokončit microsoftem nebo správcem tenanta Azure.

## <a name="create-server-application"></a>Vytvoření serverové aplikace

První aplikaci Azure AD slouží k získání členství ve skupině uživatelů Azure AD.

1. Vyberte položky **Azure Active Directory** > **Registrace aplikací** > **Registrace nové aplikace**.

  Pojmenujte aplikaci, vyberte **webovou aplikaci nebo API** pro typ aplikace a zadejte libovolnou hodnotu ve formátu identifikátoru URI pro **přihlašovací adresa URL**. Vyberte **vytvořit** až budete hotovi.

  ![Vytvoření registrace služby Azure AD](media/aad-integration/app-registration.png)

2. Vyberte **Manifest** a upravit `groupMembershipClaims` hodnota, která se `"All"`.

  Aktualizace po dokončení uložte.

  ![Aktualizovat členství ve skupině pro všechny](media/aad-integration/edit-manifest.png)

3. Zpět v aplikaci Azure AD, vyberte **nastavení** > **klíče**.

  Přidat popis klíče, vyberte termín vypršení platnosti a vyberte **Uložit**. Poznamenejte si hodnotu klíče. Při nasazení Azure AD povolené clusteru AKS, tato hodnota se označuje jako `Server application secret`.

  ![Získání privátní klíč aplikace](media/aad-integration/application-key.png)

4. Vraťte se do aplikace Azure AD, vyberte **nastavení** > **požadovaná oprávnění** > **přidat**  >   **Vyberte rozhraní API** > **Microsoft Graphu** > **vyberte**.

  ![Vyberte rozhraní graph API](media/aad-integration/graph-api.png)

5. V části **oprávnění aplikace** přidejte zaškrtnutí vedle **čtení dat adresáře**.

  ![Nastavte oprávnění ke graphu aplikace](media/aad-integration/read-directory.png)

6. V části **DELEGOVANÁ oprávnění**, přidejte zaškrtnutí vedle **přihlášení a čtení profilu uživatele** a **čtení dat adresáře**. Uložte změny, až to bude hotové.

  ![Nastavte oprávnění ke graphu aplikace](media/aad-integration/delegated-permissions.png)

7. Vyberte **provádí**, zvolte *Microsoft Graphu* ze seznamu rozhraní API, vyberte **udělit oprávnění**. Tento krok selže, pokud není aktuální účet správce tenanta.

  ![Nastavte oprávnění ke graphu aplikace](media/aad-integration/grant-permissions.png)

8. Vraťte se do aplikace a poznamenejte si **ID aplikace**. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se označuje jako `Server application ID`.

  ![Získání ID aplikace](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Vytvořit klientskou aplikaci

Druhá aplikace Azure AD se používá při přihlášení s využitím rozhraní příkazového řádku Kubernetes (kubectl).

1. Vyberte položky **Azure Active Directory** > **Registrace aplikací** > **Registrace nové aplikace**.

  Pojmenujte aplikaci, vyberte **nativní** pro typ aplikace a zadejte libovolnou hodnotu ve formátu identifikátoru URI pro **identifikátor URI pro přesměrování**. Vyberte **vytvořit** až budete hotovi.

  ![Vytvoření registrace AAD](media/aad-integration/app-registration-client.png)

2. V aplikaci Azure AD, vyberte **nastavení** > **požadovaná oprávnění** > **přidat** > **vybrat Rozhraní API** a vyhledávání pro název serveru aplikace vytvořené v předchozím kroku tohoto dokumentu.

  ![Konfigurace oprávnění aplikace](media/aad-integration/select-api.png)

3. Zaškrtněte políčko vedle aplikace a klikněte na tlačítko **vyberte**.

  ![Vyberte koncový bod aplikace AKS AAD serveru](media/aad-integration/select-server-app.png)

4. Vyberte **provádí** a **udělit oprávnění** k dokončení tohoto kroku.

  ![Udělení oprávnění](media/aad-integration/grant-permissions-client.png)

5. Zpět na aplikace AD, poznamenejte si **ID aplikace**. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se označuje jako `Client application ID`.

  ![Získání ID aplikace](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Získání ID tenanta

A konečně Získejte ID vašeho tenanta Azure. Tato hodnota se také používá při nasazování clusteru AKS.

Na webu Azure Portal, vyberte **Azure Active Directory** > **vlastnosti** a poznamenejte si **ID adresáře**. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se označuje jako `Tenant ID`.

![Získání ID tenanta Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Nasazení clusteru

Použití [vytvořit skupiny az] [ az-group-create] příkazu vytvořte skupinu prostředků pro AKS clusteru.

```azurecli
az group create --name myAKSCluster --location eastus
```

Nasazení clusteru pomocí [az aks vytvořit] [ az-aks-create] příkazu. Hodnoty v následující ukázkový příkaz nahraďte hodnoty shromážděné při vytváření aplikací v Azure AD.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Vytvoření vazby RBAC

Předtím, než účet služby Azure Active Directory je možné s clusterem AKS, role vazby nebo vazby role clusteru je potřeba vytvořit.

Nejprve [az aks get-credentials] [ az-aks-get-credentials] příkazů `--admin` argument měl přístup ke clusteru s přístupem správce.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

V dalším kroku použijte následující manifest k vytvoření ClusterRoleBinding pro účet služby Azure AD. Aktualizujte uživatelské jméno s jedním z vašeho tenanta Azure AD. V tomto příkladu poskytuje úplný přístup k účtu na všechny obory názvů clusteru.

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

Vazba role můžete také vytvořit pro všechny členy skupiny Azure AD. Skupiny Azure AD jsou určeny pomocí ID skupiny objektů.

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
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Další informace o zabezpečení clusteru Kubernetes pomocí RBAC najdete v tématu [pomocí RBAC se podařilo Autorizovat][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Přístup k clusteru se službou Azure AD

V dalším kroku o přijetí změn kontextu pro uživatele bez oprávnění správce s využitím [az aks get-credentials] [ az-aks-get-credentials] příkazu.

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

Po spuštění libovolného příkazu kubectl, budou vyzváni k ověření pomocí Azure. Použijte na obrazovce pokyny.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

Jakmile budete hotovi, je uložit do mezipaměti ověřovací token. Jsou pouze získat přihlásit, pokud vypršela platnost tokenu nebo znovu vytvořit konfigurační soubor Kubernetes.

Pokud po úspěšném přihlášení se zobrazuje zprávy o chybě autorizace, podívejte se, že uživatel se přihlašujete jako není hostovaný ve službě Azure AD (to se často stává případě Pokud používáte federované přihlašování z jiného adresáře).
```console
error: You must be logged in to the server (Unauthorized)
```


## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení clusterů Kubernetes pomocí RBAC s [pomocí RBAC se podařilo Autorizovat] [ rbac-authorization] dokumentaci.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
