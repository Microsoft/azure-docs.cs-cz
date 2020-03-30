---
title: Integrace Azure Active Directory se službou Azure Kubernetes Service
description: Jak vytvořit clustery služby Azure Kubernetes (Azure Kubernetes) s podporou Azure Active Directory
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: 0476acadf5af3a3e2c470fe6c08ebbd355653e22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596585"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrace Azure Active Directory se službou Azure Kubernetes Service

Azure Kubernetes Service (AKS) lze nakonfigurovat tak, aby pro ověřování uživatelů používala Azure Active Directory (Azure AD). V této konfiguraci se můžete přihlásit ke clusteru AKS pomocí ověřovacího tokenu Azure AD.

Správci clusteru mohou konfigurovat řízení přístupu na základě rolí Kubernetes (RBAC) na základě členství uživatele v identitě nebo adresářové skupině.

Tento článek vysvětluje, jak:

- Nasaďte požadavky pro AKS a Azure AD.
- Nasazení clusteru s podporou Azure AD.
- Vytvořte základní roli RBAC v clusteru AKS pomocí portálu Azure.

Tyto kroky můžete také provést pomocí [příkazového příkazu k příkazu Azure][azure-ad-cli].

> [!NOTE]
> Azure AD lze povolit pouze při vytvoření nového clusteru s podporou RBAC. Azure AD nelze povolit v existujícím clusteru AKS.

## <a name="authentication-details"></a>Podrobnosti o ověřování

Ověřování Azure AD se poskytuje clusterům AKS, které mají OpenID Connect. OpenID Connect je vrstva identity postavená na protokolu OAuth 2.0.

Další informace o OpenID Connect najdete [v tématu Autorizace přístupu k webovým aplikacím pomocí OpenID Connect a Azure AD][open-id-connect].

V clusteru Kubernetes se ověřování tokenů webhooku používá k ověřovacím tokenům. Ověřování tokenů Webhooku je nakonfigurováno a spravováno jako součást clusteru AKS.

Další informace o ověřování tokenů webhooku naleznete v části [Webhook Token Authentication][kubernetes-webhook] v dokumentaci Kubernetes.

Chcete-li poskytnout ověřování Azure AD pro cluster AKS, jsou vytvořeny dvě aplikace Azure AD. První aplikace je serverová součást, která poskytuje ověřování uživatelů. Druhá aplikace je klientská součást, která se používá, když se zobrazí výzva příkazového řádku pro ověřování. Tato klientská aplikace používá serverovou aplikaci pro skutečné ověření pověření poskytnutých klientem.

> [!NOTE]
> Při konfiguraci Azure AD pro ověřování AKS, dvě aplikace Azure AD jsou nakonfigurované. Kroky delegování oprávnění pro každou aplikaci musí být dokončena správcem tenanta Azure.

## <a name="create-the-server-application"></a>Vytvoření serverové aplikace

První aplikace Azure AD se použije k získání členství ve skupině Azure AD uživatele. Vytvoření této aplikace na webu Azure Portal:

1. Vyberte**Registrace** > aplikací **Azure Active Directory** > **Nová registrace**.

    a. Pojmenujte aplikaci, například *AKSAzureADServer*.

    b. U **podporovaných typů účtů**vyberte možnost Účty pouze v tomto **organizačním adresáři**.
    
    c. Zvolte **web** pro typ identifikátoru URI přesměrování a zadejte *https://aksazureadserver*libovolnou hodnotu ve formátu URI, například .

    d. Po dokončení vyberte **Registrovat.**

2. Vyberte **manifest**a upravte **skupinuDeklarace členství:** value as **All**. Až dokončíte aktualizace, vyberte **Uložit**.

    ![Aktualizovat členství ve skupině na všechny](media/aad-integration/edit-manifest.png)

3. V levém podokně aplikace Azure AD vyberte **certifikáty & tajných kódů**.

    a. Vyberte **možnost + Nový tajný klíč klienta**.

    b. Přidejte popis klíče, například *server AKS Azure AD*. Zvolte čas vypršení platnosti a pak vyberte **Přidat**.

    c. Poznamenejte si hodnotu klíče, která se zobrazí pouze v tomto okamžiku. Když nasadíte cluster AKS s podporou Azure AD, tato hodnota se nazývá tajný klíč aplikace serveru.

4. V levém podokně aplikace Azure AD vyberte **oprávnění rozhraní API**a pak vyberte + Přidat **oprávnění**.

    a. V **části Microsoft API**vyberte Microsoft **Graph**.

    b. Vyberte **Delegovaná oprávnění**a zaškrtněte políčko vedle **adresáře > Directory.Read.All (Číst data adresáře).**

    c. Pokud výchozí delegované oprávnění pro **uživatele > User.Read (Přihlásit se a číst profil uživatele)** neexistuje, zaškrtněte políčko vedle něj.

    d. Vyberte **oprávnění aplikace**a zaškrtněte políčko vedle **položky Directory > Directory.Read.All (Číst data adresáře).**

    ![Nastavení oprávnění grafu](media/aad-integration/graph-permissions.png)

    e. Vyberte **Přidat oprávnění** k uložení aktualizací.

    f. V části **Udělení souhlasu**vyberte **Udělit souhlas správce**. Toto tlačítko nebude k dispozici, že běžný účet, který se používá, není uveden jako správce klienta.

    Pokud jsou oprávnění úspěšně udělena, zobrazí se na portálu následující oznámení:

   ![Oznámení o udělených úspěšných oprávněních](media/aad-integration/permissions-granted.png)

5. V levém podokně aplikace Azure AD vyberte **vystavit rozhraní API**a pak vyberte + Přidat **obor**.
    
    a. Zadejte **název oboru**, **zobrazovaný název souhlasu správce**a potom popis **souhlasu správce,** například *AKSAzureADServer*.

    b. Ujistěte **se,** že je stav nastaven na **povoleno**.

    ![Vystavit serverovou aplikaci jako rozhraní API pro použití s jinými službami](media/aad-integration/expose-api.png)

    c. Vyberte **přidat obor**.

6. Vraťte se na stránku **Přehled** aplikace a poznamenejte si **ID aplikace (klienta).** Když nasadíte cluster AKS s podporou Azure AD, tato hodnota se nazývá ID serverové aplikace.

    ![Získat ID aplikace](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Vytvoření klientské aplikace

Druhá aplikace Azure AD se používá při přihlášení pomocí kubernetes CLI (kubectl).

1. Vyberte**Registrace** > aplikací **Azure Active Directory** > **Nová registrace**.

    a. Pojmenujte aplikaci, například *AKSAzureADClient*.

    b. U **podporovaných typů účtů**vyberte možnost Účty pouze v tomto **organizačním adresáři**.

    c. Vyberte **web** pro typ identifikátoru URI přesměrování a zadejte *https://aksazureadclient*libovolnou hodnotu ve formátu URI, například .

    >[!NOTE]
    >Pokud vytváříte nový cluster s podporou RBAC pro podporu Azure Monitor pro kontejnery, přidejte následující dvě další adresy URL přesměrování do tohoto seznamu jako typy **webových** aplikací. První základní hodnota adresy `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` URL by měla být `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`a druhá základní hodnota adresy URL by měla být .
    >
    >Pokud používáte tuto funkci v Azure China, první `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` základní hodnota adresy URL `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`by měla být a druhá základní hodnota adresy URL by měla být .
    >
    >Další informace najdete v [tématu Jak nastavit funkci Živá data (preview)](../azure-monitor/insights/container-insights-livedata-setup.md) pro Azure Monitor pro kontejnery a postup konfigurace ověřování v části [Konfigurace integrovaného ověřování služby AD.](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)

    d. Po dokončení vyberte **Registrovat.**

2. V levém podokně aplikace Azure AD vyberte **oprávnění rozhraní API**a pak vyberte + Přidat **oprávnění**.

    a. Vyberte **moje api a**pak zvolte aplikaci serveru Azure AD vytvořenou v předchozím kroku, například *AKSAzureADServer*.

    b. Vyberte **Delegovaná oprávnění**a zaškrtněte políčko vedle aplikace serveru Azure AD.

    ![Konfigurace oprávnění aplikací](media/aad-integration/select-api.png)

    c. Vyberte **Přidat oprávnění**.

    d. V části **Udělení souhlasu**vyberte **Udělit souhlas správce**. Toto tlačítko není dostupné, pokud aktuální účet není správcem tenanta. Po udělení oprávnění se na portálu zobrazí následující oznámení:

    ![Oznámení o udělených úspěšných oprávněních](media/aad-integration/permissions-granted.png)

3. V levém podokně aplikace Azure AD vyberte **Ověřování**.

    - V části **Výchozí typ klienta**vyberte **Možnost Ano,** **chcete-li klienta považovat za veřejného klienta**.

5. V levém podokně aplikace Azure AD si poznamenejte ID aplikace. Když nasadíte cluster AKS s podporou Azure AD, tato hodnota se nazývá ID klientské aplikace.

   ![Získání ID aplikace](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Získání ID klienta

Dále získejte ID vašeho tenanta Azure. Tato hodnota se používá při vytváření clusteru AKS.

Na webu Azure Portal vyberte**Vlastnosti Služby** **Azure Active Directory** > a poznamenejte si **ID adresáře**. Když vytvoříte cluster AKS s podporou Azure AD, tato hodnota se nazývá ID klienta.

![Získání ID klienta Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Nasazení clusteru AKS

Pomocí příkazu [az group create][az-group-create] vytvořte skupinu prostředků pro cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [az aks create][az-aks-create] nasadit cluster AKS. Dále nahraďte hodnoty v následujícím ukázkovém příkazu. Použijte hodnoty shromážděné při vytváření aplikací Azure AD pro ID serverové aplikace, tajný klíč aplikace, ID klientské aplikace a ID klienta.

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
> Název vazby role clusteru je rozlišován malá a velká písmena.

Před použitím účtu Služby Azure Active Directory s clusterem AKS je nutné vytvořit vazbu role nebo vazbu role clusteru. Role definují oprávnění k udělení a vazby je aplikují na požadované uživatele. Tato přiřazení lze použít pro daný obor názvů nebo v celém clusteru. Další informace naleznete [v tématu Použití autorizace RBAC][rbac-authorization].

Nejprve použijte příkaz [az aks get-credentials][az-aks-get-credentials] s argumentem `--admin` pro přihlášení do clusteru s přístupem správce.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Dále vytvořte ClusterRoleBinding pro účet Azure AD, který chcete udělit přístup ke clusteru AKS. Následující příklad poskytuje účtu úplný přístup ke všem oborům názvů v clusteru:

- Pokud uživatel, pro kterého udělíte vazbu RBAC, je ve stejném tenantovi Azure AD, přiřaďte oprávnění na základě hlavního názvu uživatele (UPN). Přejděte ke kroku a vytvořte manifest YAML pro clusterrolebinding.

- Pokud je uživatel v jiném tenantovi Azure AD, dotaz a místo toho použít **vlastnost objectId.** V případě potřeby získejte objektId požadovaného uživatelského účtu pomocí příkazu [az ad user show.][az-ad-user-show] Zadejte hlavní uživatelské jméno (UPN) požadovaného účtu:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Vytvořte soubor, například *rbac-aad-user.yaml*, a vložte následující obsah. Na posledním řádku nahraďte **userPrincipalName_or_objectId** název hlavního název nebo ID objektu. Volba závisí na tom, zda je uživatel stejný klient Azure AD nebo ne.

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

Použijte vazbu pomocí příkazu [kubectl apply,][kubectl-apply] jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-user.yaml
```

Vazbu role lze také vytvořit pro všechny členy skupiny Azure AD. Skupiny Azure AD jsou určeny pomocí ID objektu skupiny, jak je znázorněno v následujícím příkladu.

Vytvořte soubor, například *rbac-aad-group.yaml*, a vložte následující obsah. Aktualizujte ID objektu skupiny pomocí jednoho z vašeho klienta Azure AD:

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

Použijte vazbu pomocí příkazu [kubectl apply,][kubectl-apply] jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-group.yaml
```

Další informace o zabezpečení clusteru Kubernetes pomocí RBAC naleznete [v tématu Použití autorizace RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Přístup ke clusteru pomocí Azure AD

Vyžádejte kontext pro uživatele bez správce pomocí příkazu [az aks get-credentials.][az-aks-get-credentials]

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po spuštění `kubectl` příkazu budete vyzváni k ověření pomocí Azure. Dokončete proces podle pokynů na obrazovce, jak je znázorněno v následujícím příkladu:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Po dokončení procesu je ověřovací token uložen do mezipaměti. Budete vyzváni k opětovnému přihlášení až po vypršení platnosti tokenu nebo opětovném vytvoření konfiguračního souboru Kubernetes.

Pokud se po úspěšném přihlášení zobrazí chybová zpráva autorizace, zkontrolujte následující kritéria:

```console
error: You must be logged in to the server (Unauthorized)
```


- Definovali jste id příslušného objektu nebo hlavní název uživatele, v závislosti na tom, jestli je uživatelský účet ve stejném tenantovi Azure AD nebo ne.
- Uživatel není členem více než 200 skupin.
- Tajný klíč definovaný v registraci aplikace pro `--aad-server-app-secret`server odpovídá hodnotě nakonfigurované pomocí .

## <a name="next-steps"></a>Další kroky

Pokud chcete k řízení přístupu k prostředkům clusteru používat uživatele a skupiny Azure AD k řízení přístupu k prostředkům clusteru, [přečtěte si témat řízení přístupu k prostředkům clusteru pomocí řízení přístupu na základě rolí a identit Azure AD v AKS][azure-ad-rbac].

Další informace o zabezpečení clusterů Kubernetes naleznete v [tématu Access and identity options for AKS][rbac-authorization].

Další informace o řízení identity a prostředků najdete [v tématu Doporučené postupy pro ověřování a autorizaci v AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
