---
title: Integrace Azure Active Directory se službou Azure Kubernetes
description: Postup vytvoření clusterů Azure Kubernetes Service (AKS) s podporou Azure Active Directory
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 26f1544cab5cf5be2edd52f97c758d46eb835514
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103792"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrace Azure Active Directory se službou Azure Kubernetes

Službu Azure Kubernetes Service (AKS) je možné nakonfigurovat tak, aby pro ověřování uživatelů používala Azure Active Directory (Azure AD). V této konfiguraci se můžete přihlásit ke clusteru AKS pomocí ověřovacího tokenu Azure AD.

Správci clusteru můžou nakonfigurovat řízení přístupu na základě role (RBAC) Kubernetes na základě identity uživatele nebo členství ve skupině adresáře.

V tomto článku se dozvíte, jak:

- Nasaďte požadavky pro AKS a Azure AD.
- Nasaďte cluster s podporou Azure AD.
- K vytvoření základní role RBAC v clusteru AKS použijte Azure Portal.

Tyto kroky můžete provést také pomocí rozhraní příkazového [řádku Azure CLI][azure-ad-cli].

> [!NOTE]
> Azure AD se dá povolit, jenom když vytvoříte nový cluster s podporou RBAC. Službu Azure AD nelze povolit v existujícím clusteru AKS.

## <a name="authentication-details"></a>Podrobnosti ověřování

K AKS clusterům, které mají OpenID připojení, se poskytuje ověřování Azure AD. OpenID Connect je vrstva identity postavená nad protokolem OAuth 2,0.

Další informace o OpenID Connect najdete v tématu [autorizace přístupu k webovým aplikacím pomocí OpenID Connect a Azure AD][open-id-connect].

V rámci clusteru Kubernetes se ověřování pomocí tokenu Webhooku používá pro ověřovací tokeny. Ověřování tokenu Webhooku je nakonfigurované a spravované jako součást clusteru AKS.

Další informace o ověřování tokenů Webhooku najdete v části [ověřování tokenu Webhooku][kubernetes-webhook] v dokumentaci k Kubernetes.

Aby bylo možné zajistit ověřování Azure AD pro cluster AKS, vytvoří se dvě aplikace Azure AD. První aplikací je serverová součást, která poskytuje ověřování uživatelů. Druhá aplikace je klientská komponenta, která se používá v případě, že rozhraní příkazového řádku vyzve k ověření. Tato klientská aplikace používá serverovou aplikaci pro skutečné ověření přihlašovacích údajů poskytnutých klientem.

> [!NOTE]
> Při konfiguraci služby Azure AD pro ověřování AKS jsou nakonfigurované dvě aplikace Azure AD. Kroky pro delegování oprávnění pro jednotlivé aplikace musí dokončit správce tenanta Azure.

## <a name="create-the-server-application"></a>Vytvoření serverové aplikace

Použije se první aplikace Azure AD, která získá členství uživatele ve skupině Azure AD. Chcete-li vytvořit tuto aplikaci v Azure Portal:

1. Vyberte **Azure Active Directory** > registrace aplikacínovou > **registraci**.

    a. Zadejte název aplikace, například *AKSAzureADServer*.

    b. U **podporovaných typů účtů**vyberte **účty jenom v tomto organizačním adresáři**.
    
    c. Jako typ URI přesměrování zvolte **Web** a potom zadejte libovolnou hodnotu formátovanou identifikátorem URI, například *https://aksazureadserver* .

    d. Až budete hotovi, vyberte **zaregistrovat** .

2. Vyberte **manifest**a pak upravte **groupMembershipClaims:** value jako **vše**. Až budete s aktualizacemi hotovi, vyberte **Uložit**.

    ![Aktualizovat členství ve skupině na všechny](media/aad-integration/edit-manifest.png)

3. V levém podokně aplikace Azure AD vyberte **certifikáty & tajných**kódů.

    a. Vyberte **+ nový tajný klíč klienta**.

    b. Přidejte popis klíče, jako je například *AKS Azure AD Server*. Zvolte čas vypršení platnosti a pak vyberte **Přidat**.

    c. Poznamenejte si hodnotu klíče, která se zobrazí pouze v tomto okamžiku. Když nasadíte cluster AKS s podporou Azure AD, tato hodnota se nazývá tajný kód serverové aplikace.

4. V levém podokně aplikace Azure AD vyberte **oprávnění rozhraní API**a pak vyberte **+ Přidat oprávnění**.

    a. V části **Microsoft API**vyberte **Microsoft Graph**.

    b. Vyberte **delegovaná oprávnění**a potom zaškrtněte políčko vedle **adresáře Directory > Directory. Read. All (čtení dat adresáře)** .

    c. Pokud výchozí delegované oprávnění pro **uživatele > uživatel. čtení (přihlášení a čtení profilu uživatele)** neexistuje, zaškrtněte políčko vedle něj.

    d. Vyberte **oprávnění aplikace**a potom zaškrtněte políčko vedle **adresáře Directory > Directory. Read. All (čtení dat adresáře)** .

    ![Nastavení oprávnění grafu](media/aad-integration/graph-permissions.png)

    e. Pokud chcete aktualizace uložit, vyberte **Přidat oprávnění** .

    f. V části **souhlas udělení souhlasu**vyberte **udělit souhlas správce**. Toto tlačítko nebude dostupné. aktuálně používaný účet není uvedený jako správce tenanta.

    Po úspěšném udělení oprávnění se na portálu zobrazí následující oznámení:

   ![Oznámení o úspěšných oprávněních udělených](media/aad-integration/permissions-granted.png)

5. V levém podokně aplikace Azure AD vyberte **zveřejnit rozhraní API**a pak vyberte **+ Přidat obor**.
    
    a. Zadejte **název oboru**, **Zobrazovaný název souhlasu správce**a pak **Popis souhlasu správce** , jako je *AKSAzureADServer*.

    b. Ujistěte se, že je **stav** nastavený na **povoleno**.

    ![Zveřejnění serverové aplikace jako rozhraní API pro použití s dalšími službami](media/aad-integration/expose-api.png)

    c. Vyberte **Přidat obor**.

6. Vraťte se na stránku **Přehled** aplikace a poznamenejte si **ID aplikace (klienta)** . Když nasadíte cluster AKS s podporou Azure AD, tato hodnota se nazývá ID serverové aplikace.

    ![Získat ID aplikace](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Vytvoření klientské aplikace

Druhá aplikace Azure AD se používá v případě, že se přihlašujete pomocí rozhraní příkazového řádku Kubernetes (kubectl).

1. Vyberte **Azure Active Directory** > registrace aplikacínovou > **registraci**.

    a. Zadejte název aplikace, například *AKSAzureADClient*.

    b. U **podporovaných typů účtů**vyberte **účty jenom v tomto organizačním adresáři**.

    c. Jako typ URI přesměrování vyberte **Web** a potom zadejte libovolnou hodnotu formátovanou identifikátorem URI, například *https://aksazureadclient* .

    d. Až budete hotovi, vyberte **zaregistrovat** .

2. V levém podokně aplikace Azure AD vyberte **oprávnění rozhraní API**a pak vyberte **+ Přidat oprávnění**.

    a. Vyberte **Moje rozhraní API**a pak zvolte aplikaci Azure AD Server vytvořenou v předchozím kroku, například *AKSAzureADServer*.

    b. Vyberte **delegovaná oprávnění**a potom zaškrtněte políčko vedle své aplikace serveru Azure AD.

    ![Konfigurace oprávnění aplikace](media/aad-integration/select-api.png)

    c. Vyberte **Přidat oprávnění**.

    d. V části **souhlas udělení souhlasu**vyberte **udělit souhlas správce**. Toto tlačítko není k dispozici, pokud aktuální účet není správcem tenanta. Po udělení oprávnění se na portálu zobrazí následující oznámení:

    ![Oznámení o úspěšných oprávněních udělených](media/aad-integration/permissions-granted.png)

3. V levém podokně aplikace Azure AD vyberte **ověřování**.

    - V části **výchozí typ klienta**vyberte **Ano** , pokud chcete **klienta považovat za veřejného klienta**.

5. V levém podokně aplikace Azure AD si poznamenejte ID aplikace. Když nasadíte cluster AKS s podporou Azure AD, tato hodnota se nazývá ID klientské aplikace.

   ![Získat ID aplikace](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Získat ID tenanta

Potom Získejte ID vašeho tenanta Azure. Tato hodnota se používá při vytváření clusteru AKS.

Z Azure Portal vyberte **Azure Active Directory** > **vlastnosti** a poznamenejte si **ID adresáře**. Když vytvoříte cluster AKS s podporou Azure AD, tato hodnota se nazývá ID tenanta.

![Získat ID tenanta Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Nasazení clusteru AKS

Pomocí příkazu [AZ Group Create][az-group-create] vytvořte skupinu prostředků pro cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [AZ AKS Create][az-aks-create] nasaďte cluster AKS. Dále nahraďte hodnoty v následujícím ukázkovém příkazu. Použijte hodnoty shromážděné při vytváření aplikací Azure AD pro ID aplikace serveru, tajný klíč aplikace, ID klientské aplikace a ID tenanta.

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

Vytvoření clusteru AKS trvá několik minut.

## <a name="create-an-rbac-binding"></a>Vytvoření vazby RBAC

> [!NOTE]
> V názvu vazby role clusteru se rozlišují velká a malá písmena.

Předtím, než použijete účet Azure Active Directory s clusterem AKS, je nutné vytvořit vazbu role nebo vazby role clusteru. Role definují oprávnění pro udělení a vazby je použijí pro požadované uživatele. Tato přiřazení lze použít na daný obor názvů nebo v celém clusteru. Další informace najdete v tématu [použití autorizace RBAC][rbac-authorization].

Nejprve pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] s `--admin` argumentem se přihlaste ke clusteru s přístupem správce.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

V dalším kroku vytvořte ClusterRoleBinding pro účet Azure AD, který chcete udělit přístup ke clusteru AKS. Následující příklad poskytne účtu úplný přístup ke všem oborům názvů v clusteru:

- Pokud se uživateli, kterému udělíte vazbu RBAC, nachází ve stejném tenantovi Azure AD, přiřaďte oprávnění na základě hlavního názvu uživatele (UPN). Přejděte k kroku a vytvořte YAML manifest pro ClusterRoleBinding.

- Pokud je uživatel v jiném tenantovi služby Azure AD, dotaz na a místo toho použijte vlastnost **objectID** . V případě potřeby získejte identifikátor objectId požadovaného uživatelského účtu pomocí příkazu [AZ AD User show][az-ad-user-show] . Zadejte hlavní název uživatele (UPN) požadovaného účtu:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Vytvořte soubor, například *RBAC-AAD-User. yaml*, a vložte následující obsah. Na posledním řádku nahraďte **userPrincipalName_or_objectId** hlavní název uživatele (UPN) nebo ID objektu. Volba závisí na tom, jestli je uživatel stejný tenant služby Azure AD.

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

Použijte vazbu pomocí příkazu [kubectl Apply][kubectl-apply] , jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-user.yaml
```

Vazba role se dá vytvořit taky pro všechny členy skupiny Azure AD. Skupiny Azure AD se zadává pomocí ID objektu skupiny, jak je znázorněno v následujícím příkladu.

Vytvořte soubor, například *RBAC-AAD-Group. yaml*, a vložte následující obsah. Aktualizujte ID objektu skupiny pomocí některého z tenanta Azure AD:

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

Použijte vazbu pomocí příkazu [kubectl Apply][kubectl-apply] , jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-group.yaml
```

Další informace o zabezpečení clusteru Kubernetes pomocí RBAC najdete v tématu [použití autorizace RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Přístup ke clusteru pomocí Azure AD

Pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] načtěte kontext pro uživatele bez oprávnění správce.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po spuštění `kubectl` příkazu se zobrazí výzva k ověření pomocí Azure. Dokončete proces podle pokynů na obrazovce, jak je znázorněno v následujícím příkladu:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Po dokončení procesu se ověřovací token uloží do mezipaměti. Po vypršení platnosti tokenu se zobrazí výzva, abyste se znovu přihlásili, nebo se konfigurační soubor Kubernetes znovu vytvoří.

Pokud se vám po úspěšném přihlášení zobrazí chybová zpráva o autorizaci, zkontrolujte následující kritéria:

```console
error: You must be logged in to the server (Unauthorized)
```


- V závislosti na tom, jestli je uživatelský účet ve stejném tenantovi Azure AD, jste definovali příslušné ID objektu nebo hlavní název uživatele (UPN).
- Uživatel není členem více než 200 skupin.
- Tajný kód definovaný v registraci aplikace pro server se shoduje s hodnotou konfigurovanou pomocí `--aad-server-app-secret`.

## <a name="next-steps"></a>Další kroky

Postup použití uživatelů a skupin Azure AD k řízení přístupu k prostředkům clusteru najdete v tématu [řízení přístupu k prostředkům clusteru pomocí řízení přístupu založeného na rolích a identit Azure AD v AKS][azure-ad-rbac].

Další informace o tom, jak zabezpečit clustery Kubernetes, najdete v tématu [Možnosti přístupu a identit pro AKS][rbac-authorization].

Další informace o identitě a řízení prostředků najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

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
