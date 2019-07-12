---
title: Integrace služby Azure Active Directory s Azure Kubernetes Service
description: Vytvoření clusterů s podporou Azure Active Directory Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616391"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrace služby Azure Active Directory s Azure Kubernetes Service

Azure Kubernetes Service (AKS) by šlo použít Azure Active Directory (Azure AD) pro ověřování uživatelů. V této konfiguraci se můžete přihlásit AKS cluster pomocí tokenu ověřování Azure AD.

Správce clusteru můžete nakonfigurovat Kubernetes řízení přístupu na základě rolí (RBAC) na základě členství ve skupině uživatelské identity nebo adresáře.

Tento článek vysvětluje, jak:

- Nasazení nezbytných součástí pro Azure AD a AKS.
- Nasazení clusteru Azure AD povolené.
- Vytvoření základní role RBAC v clusteru AKS pomocí webu Azure portal.

Dokončení těchto kroků můžete také pomocí [rozhraní příkazového řádku Azure][azure-ad-cli].

> [!NOTE]
> Azure AD jde Povolit jenom při vytváření nového clusteru s podporou RBAC. Nejde povolit Azure AD v existujícím clusteru AKS.

## <a name="authentication-details"></a>Podrobnosti o ověřování

Ověřování Azure AD je k dispozici pro AKS clustery, které mají OpenID Connect. OpenID Connect se vrstvu identit postavené na protokol OAuth 2.0.

Další informace o OpenID Connect najdete v tématu [autorizaci přístupu k webovým aplikacím pomocí OpenID Connect a službou Azure AD][open-id-connect].

V clusteru Kubernetes ověřování pomocí tokenu webhooku slouží k ověřování tokenů. Ověřování pomocí tokenu Webhooku je nakonfigurovat a spravovat jako součást clusteru AKS.

Další informace o ověřování pomocí tokenu webhooku, najdete v článku [ověřovací Token Webhooku][kubernetes-webhook] oddílu v dokumentaci ke Kubernetes.

K zajištění ověřování Azure AD pro AKS cluster, se vytvoří dvě aplikace Azure AD. První aplikaci je součást serveru, který poskytuje ověřování uživatelů. Druhá aplikace je klientské komponenty, která se používá, když se zobrazí výzva k rozhraní příkazového řádku pro ověřování. Tato klientská aplikace používá serverová aplikace skutečný ověřování přihlašovací údaje poskytnuté klientem.

> [!NOTE]
> Při konfiguraci Azure AD pro ověřování AKS jsou nakonfigurovány dvě aplikace Azure AD. Microsoftem nebo správcem tenanta Azure musíte dokončit postup delegování oprávnění pro každou aplikaci.

## <a name="create-the-server-application"></a>Vytvoření serverové aplikace

První aplikaci Azure AD se použije k získání uživatele členství ve skupině Azure AD. Chcete-li vytvořit tuto aplikaci na webu Azure Portal:

1. Vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové**.

    a. Pojmenujte aplikaci, jako například *AKSAzureADServer*.

    b. Pro **podporovaných typů účtu**vyberte **účty v tomto adresáři organizace jenom**.
    
    c. Zvolte **webové** pro identifikátor URI přesměrování, zadejte a potom zadejte libovolnou hodnotu ve formátu identifikátoru URI, třeba *https://aksazureadserver* .

    d. Vyberte **zaregistrovat** až budete hotoví.

2. Vyberte **Manifest**a pak upravte **groupMembershipClaims:** hodnotu **všechny**. Jakmile budete hotovi s aktualizacemi, vyberte **Uložit**.

    ![Aktualizovat členství ve skupině pro všechny](media/aad-integration/edit-manifest.png)

3. V levém podokně aplikace Azure AD, vyberte **certifikáty a tajné kódy**.

    a. Vyberte **+ nový tajný kód klienta**.

    b. Přidat popis klíče, jako například *AKS Azure AD server*. Zvolte čas vypršení platnosti a pak vyberte **přidat**.

    c. Poznamenejte si hodnotu klíče, který se zobrazí pouze v tuto chvíli. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se nazývá tajný klíč aplikace serveru.

4. V levém podokně aplikace Azure AD, vyberte **oprávnění k rozhraní API**a pak vyberte **+ přidat oprávnění**.

    a. V části **Microsoft APIs**vyberte **Microsoft Graphu**.

    b. Vyberte **delegovaná oprávnění**a potom zaškrtněte políčko vedle položky **adresář > Directory.Read.All (čtení dat adresáře)** .

    c. Pokud výchozí delegovaná oprávnění pro **uživatele > User.Read (přihlášení a čtení profilu uživatele)** neexistuje, zaškrtněte políčko vedle něj.

    d. Vyberte **oprávnění aplikace**a potom zaškrtněte políčko vedle položky **adresář > Directory.Read.All (čtení dat adresáře)** .

    ![Sada oprávnění ke graphu](media/aad-integration/graph-permissions.png)

    e. Vyberte **přidat oprávnění** uložte aktualizace.

    f. V části **udělit souhlas**vyberte **udělit souhlas správce**. Toto tlačítko není k dispozici, pokud není aktuální účet správce tenanta.

    Když se úspěšně udělila oprávnění, se zobrazí následující oznámení na portálu:

   ![Oznámení o úspěšném oprávnění udělená](media/aad-integration/permissions-granted.png)

5. V levém podokně aplikace Azure AD, vyberte **vystavit rozhraní API**a pak vyberte **+ přidat nový obor**.
    
    a. Zadejte **název oboru**, **zobrazovaný název souhlasu správce**a potom **popis souhlasu správce** například *AKSAzureADServer*.

    b. Ujistěte se, že **stavu** je nastavena na **povoleno**.

    ![Zveřejnit aplikaci server jako rozhraní API pro použití s jinými službami](media/aad-integration/expose-api.png)

    c. Vyberte **přidat obor**.

6. Vraťte se do aplikace **přehled** stránky a Všimněte si **ID aplikace (klient)** . Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se nazývá ID serveru aplikace.

    ![Získání ID aplikace](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Vytvoření klientské aplikace

Druhá aplikace Azure AD se používá při přihlášení s použitím rozhraní příkazového řádku Kubernetes (kubectl).

1. Vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové**.

    a. Pojmenujte aplikaci, jako například *AKSAzureADClient*.

    b. Pro **podporovaných typů účtu**vyberte **účty v tomto adresáři organizace jenom**.

    c. Vyberte **webové** pro identifikátor URI přesměrování, zadejte a pak zadejte libovolnou hodnotu ve formátu identifikátoru URI jako *https://aksazureadclient* .

    d. Vyberte **zaregistrovat** až budete hotoví.

2. V levém podokně aplikace Azure AD, vyberte **oprávnění k rozhraní API**a pak vyberte **+ přidat oprávnění**.

    a. Vyberte **Moje rozhraní API**a klikněte na tlačítko serverovou aplikaci Azure AD vytvořili v předchozím kroku, jako například *AKSAzureADServer*.

    b. Vyberte **delegovaná oprávnění**a potom zaškrtněte políčko vedle aplikací serveru Azure AD.

    ![Konfigurace oprávnění aplikace](media/aad-integration/select-api.png)

    c. Vyberte **přidat oprávnění**.

    d. V části **udělit souhlas**vyberte **udělit souhlas správce**. Toto tlačítko není k dispozici, pokud není aktuální účet správce tenanta. Když jsou udělena oprávnění, se zobrazí následující oznámení na portálu:

    ![Oznámení o úspěšném oprávnění udělená](media/aad-integration/permissions-granted.png)

3. V levém podokně aplikace Azure AD, vyberte **ověřování**.

    - V části **výchozí typ klienta**vyberte **Ano** k **klienta považovat za veřejné klienta**.

5. V levém podokně aplikace Azure AD poznamenejte si ID aplikace. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se nazývá ID klienta aplikace.

   ![Získání ID aplikace](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Získání ID tenanta

V dalším kroku získáte ID tenanta služby Azure. Tato hodnota se používá při vytváření clusteru AKS.

Na webu Azure Portal, vyberte **Azure Active Directory** > **vlastnosti** a Všimněte si, **ID adresáře**. Při vytváření clusteru služby Azure AD povolené AKS, tato hodnota se nazývá ID tenanta.

![Získání ID tenanta Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Nasazení clusteru AKS

Použití [vytvořit skupiny az][az-group-create] příkazu vytvořte skupinu prostředků pro AKS clusteru.

```azurecli
az group create --name myResourceGroup --location eastus
```

Použití [az aks vytvořit][az-aks-create] příkaz pro nasazení clusteru AKS. V dalším kroku nahraďte hodnoty v následující ukázkový příkaz. Použijte hodnoty shromážděné při vytváření aplikací v Azure AD pro server app ID, tajný kód aplikace, ID klientské aplikace a ID tenanta.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

AKS cluster trvá několik minut vytvořit.

## <a name="create-an-rbac-binding"></a>Vytvořte vazbu RBAC

Než použijete účet služby Azure Active Directory s clusterem AKS, je nutné vytvořit vazbu role nebo role vazby clusteru. Role definují oprávnění udělit a vazby aplikovat na požadované uživatele. Tato přiřazení můžete použít k daném oboru názvů nebo přes celý cluster. Další informace najdete v tématu [pomocí RBAC se podařilo Autorizovat][rbac-authorization].

Nejprve [az aks get-credentials][az-aks-get-credentials] příkazů `--admin` argument pro přihlášení ke clusteru s přístupem správce.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Dále vytvořte ClusterRoleBinding pro účet služby Azure AD, že chcete udělit přístup ke clusteru AKS. Následující příklad poskytuje úplný přístup k účtu na všechny obory názvů v clusteru:

- Pokud uživatel udělíte, že se vazba RBAC pro stejného tenanta služby Azure AD, přiřaďte oprávnění podle principu hlavní název uživatele (UPN). Přesunout na krok k vytvoření manifestu YAML pro ClusterRoleBinding.

- Pokud se uživatel nachází v jiné službě Azure AD tenanta, dotázat a použít **objectId** vlastnost místo. V případě potřeby získat objectId vyžaduje uživatelský účet s použitím [az ad uživateli zobrazit][az-ad-user-show] příkazu. Zadejte hlavní název uživatele (UPN) požadovaný účet:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Vytvoření souboru, například *rbac-aad-user.yaml*a potom vložte následující obsah. Na posledním řádku, nahraďte **userPrincipalName_or_objectId** s ID (UPN) nebo objekt. Výběr závisí na tom, jestli je uživatel stejného tenanta služby Azure AD nebo ne.

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

Použít vazbu s použitím [použití kubectl][kubectl-apply] příkaz, jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-user.yaml
```

Vazba role můžete také vytvořit pro všechny členy skupiny Azure AD. Jsou zadané skupiny Azure AD s použitím ID objektu skupiny, jak je znázorněno v následujícím příkladu.

Vytvoření souboru, například *rbac-aad-group.yaml*a potom vložte následující obsah. Aktualizace ID objektu skupiny s jedním z vašeho tenanta Azure AD:

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

Použít vazbu s použitím [použití kubectl][kubectl-apply] příkaz, jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-group.yaml
```

Další informace o zabezpečení clusteru Kubernetes pomocí RBAC najdete v tématu [pomocí RBAC se podařilo Autorizovat][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Přístup ke clusteru pomocí Azure AD

S použitím o přijetí změn kontextu pro uživatele bez oprávnění správce [az aks get-credentials][az-aks-get-credentials] příkazu.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po spuštění `kubectl` příkaz, zobrazí se výzva k ověření pomocí služby Azure. Použijte na obrazovce pokynů a dokončete proces, jak je znázorněno v následujícím příkladu:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Po dokončení procesu se ověřovací token do mezipaměti. Pouze budete vyzváni k přihlášení akci při vypršení platnosti tokenu nebo konfigurační soubor Kubernetes nebude znovu vytvořena.

Pokud po úspěšném přihlášení se zobrazí zprávy o chybě autorizace, zkontrolujte následující kritéria:

```console
error: You must be logged in to the server (Unauthorized)
```


- Jste definovali ID odpovídající objektu nebo UPN, podle toho, zda uživatelský účet je ve stejném tenantovi Azure AD, nebo ne.
- Uživatel není členem více než 200 skupin.
- Tajný kód definovaný v registrace aplikace pro server odpovídá hodnotě nakonfigurované pomocí `--aad-server-app-secret`.

## <a name="next-steps"></a>Další postup

Řízení přístupu k prostředkům clusteru pomocí Azure AD Uživatelé a skupiny, najdete v článku [řízení přístupu k prostředkům clusteru pomocí Azure AD identity a řízení přístupu na základě role ve službě AKS][azure-ad-rbac].

Další informace o tom, jak zabezpečené clustery Kubernetes najdete v tématu [možnosti přístupu a identit pro AKS][rbac-authorization].

Další informace o řízení prostředků a identit najdete v tématu [osvědčené postupy pro ověřování a autorizace ve službě AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
