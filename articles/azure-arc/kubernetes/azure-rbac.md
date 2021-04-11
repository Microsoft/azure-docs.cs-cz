---
title: Azure RBAC pro Kubernetes clustery s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Použití služby Azure RBAC pro kontroly autorizace u clusterů Kubernetes s povolenou službou Azure ARC
ms.openlocfilehash: bd8029cb2772a6f6bd9821abe6acf69c9c08599d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450969"
---
# <a name="azure-rbac-for-azure-arc-enabled-kubernetes-clusters"></a>Azure RBAC pro Kubernetes clustery s podporou ARC Azure

Kubernetes [ClusterRoleBinding a](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) typy objektů RoleBinding usnadňují definování autorizace v Kubernetes nativně. Pomocí služby Azure RBAC můžete pomocí Azure Active Directory a přiřazení rolí v Azure řídit kontroly autorizace v clusteru. To znamená, že teď můžete pomocí přiřazení rolí Azure přesně určovat, kdo může číst, zapisovat a odstraňovat Kubernetes objekty, jako je nasazení, pod a službu.

Koncepční přehled této funkce je k dispozici v článku [Azure RBAC – Azure ARC s povoleným Kubernetes](conceptual-azure-rbac.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Požadavky

- [Instalace nebo upgrade rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na verzi >= 2.16.0

- Nainstalujte `connectedk8s` rozšíření Azure CLI verze >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Pokud `connectedk8s` je rozšíření už nainstalované, můžete ho aktualizovat na nejnovější verzi pomocí tohoto příkazu: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Existující Kubernetes připojený cluster s podporou Azure ARC.
    - Pokud jste ještě nepřipojili cluster, Využijte náš [rychlý Start](quickstart-connect-cluster.md).
    - [Upgradujte agenty](agent-upgrade.md#manually-upgrade-agents) na verzi >= 1.1.0.

> [!NOTE]
> Tato funkce se nedá nastavit pro spravované nabídky Kubernetes poskytovatelů cloudu, jako je elastická Kubernetes služba nebo modul Google Kubernetes, kde uživatel nemá přístup k `apiserver` tomuto clusteru. Pro clustery Azure Kubernetes Service (AKS) [je tato funkce dostupná nativně](../../aks/manage-azure-rbac.md) a nevyžaduje, aby byl cluster AKS připojený ke službě Azure ARC.

## <a name="set-up-azure-ad-applications"></a>Nastavení aplikací Azure AD

### <a name="create-server-application"></a>Vytvořit serverovou aplikaci

1. Vytvořte novou aplikaci Azure AD a získejte její `appId` hodnotu, která se používá v pozdějších krocích jako `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Aktualizujte deklarace identity členství ve skupině aplikací:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Vytvoření instančního objektu a získání jeho `password` hodnoty pole, která je požadována později jako `serverApplicationSecret` při povolení této funkce v clusteru:

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Udělte oprávnění Application API:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Tento krok je třeba provést správcem tenanta Azure.
    > * Pro použití této funkce v produkčním prostředí se doporučuje vytvořit pro každý cluster jinou serverovou aplikaci.

### <a name="create-client-application"></a>Vytvořit klientskou aplikaci

1. Vytvořte novou aplikaci Azure AD a získejte její hodnotu appId, která se používá v pozdějších krocích jako `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Vytvořit instanční objekt pro tuto klientskou aplikaci:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. Získat `oAuthPermissionId` pro serverovou aplikaci:

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. Udělte pro klientskou aplikaci požadovaná oprávnění:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Vytvoření přiřazení role pro serverovou aplikaci

Serverová aplikace potřebuje `Microsoft.Authorization/*/read` oprávnění, aby zkontrolovala, jestli je uživatel, který požadavek odeslal, autorizovaný na Kubernetesch objektech, které jsou součástí žádosti.

1. Vytvořte soubor s názvem accessCheck.jss následujícím obsahem:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Nahraďte `<subscription-id>` skutečným ID předplatného.

2. Spuštěním následujícího příkazu vytvořte novou vlastní roli:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. Ve výstupu výše uvedeného příkazu uložte hodnotu `id` pole, která se používá v pozdějších krocích jako `roleId` .

4. Vytvořte přiřazení role v serverové aplikaci jako pověřené pomocí role vytvořené výše:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Povolit službu Azure RBAC v clusteru

1. Spuštěním následujícího příkazu povolte v clusteru Kubernetes s povoleným ARC službu Azure RBAC:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Před spuštěním výše uvedeného příkazu zajistěte, aby `kubeconfig` soubor na počítači odkazoval na cluster, na kterém chcete povolit funkci Azure RBAC.
    > 2. Použijte `--skip-azure-rbac-list` s výše uvedeným příkazem pro čárkami oddělený seznam uživatelských jmen/e-mailů/identifikátorů OID, a to pomocí Kubernetes nativních ClusterRoleBinding a RoleBinding objektů místo Azure RBAC.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>Pro obecný cluster, ve kterém není spuštěný žádný odsouhlasený apiserver Specification:

1. Přihlaste se přes SSH do každého hlavního uzlu clusteru a proveďte následující kroky:

    1. Otevřít `apiserver` manifest v režimu úprav:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Do následujícího pole přidejte následující specifikaci `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Do následujícího pole přidejte následující specifikaci `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Přidejte následující `apiserver` argumenty:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Pokud je cluster Kubernetes verze >= 1.19.0, je `apiserver argument` nutné nastavit také následující:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Uložte a ukončete Editor a aktualizujte `apiserver` pod.


### <a name="for-a-cluster-created-using-cluster-api"></a>Pro cluster vytvořený pomocí rozhraní API clusteru

1. Zkopírujte tajný klíč Guard obsahující konfigurační soubory Webhooku pro ověřování a autorizaci `from the workload cluster` na váš počítač:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Změňte `namespace` pole v `azure-arc-guard-manifests.yaml` souboru na obor názvů v rámci clusteru pro správu, ve kterém aplikujete vlastní prostředky pro vytváření clusterů úloh.

1. Použít tento manifest:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Upravte `KubeadmControlPlane` objekt spuštěním `kubectl edit kcp <clustername>-control-plane` :
    
    1. Do tohoto pole přidejte následující fragment kódu `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Do tohoto pole přidejte následující fragment kódu `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Do tohoto pole přidejte následující fragment kódu `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Uložte a zavřete pro aktualizaci `KubeadmControlPlane` objektu. Počkejte na to, aby se tyto změny provedly na clusteru úloh.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Vytvoření přiřazení rolí pro uživatele, kteří budou mít přístup ke clusteru

Vlastníci Kubernetes prostředku Azure ARC můžou použít předdefinované role nebo vlastní role a udělit ostatním uživatelům přístup ke clusteru Kubernetes.

### <a name="built-in-roles"></a>Vestavěné role

| Role | Popis |
|---|---|
| Prohlížeč Kubernetes pro Azure ARC | Povoluje přístup jen pro čtení k zobrazení většiny objektů v oboru názvů. Tato role nepovoluje zobrazení tajných kódů. Důvodem je to `read` , že oprávnění k tajným klíčům umožní přístup k `ServiceAccount` přihlašovacím údajům v oboru názvů, což by umožnilo povolit přístup k rozhraní API pomocí tohoto `ServiceAccount` (formu eskalace oprávnění). |
| Zapisovač Azure ARC Kubernetes | Povoluje přístup pro čtení a zápis většiny objektů v oboru názvů. Tato role nepovoluje prohlížení a úpravy rolí nebo vazeb rolí. Tato role však umožňuje přístup k tajným klíčům a spouštění lusků `ServiceAccount` v oboru názvů, takže je možné ji použít k získání úrovní přístupu rozhraní API libovolného `ServiceAccount` v oboru názvů. |
| Správce Azure ARC Kubernetes | Povoluje přístup správce. Má být uděleno v rámci oboru názvů pomocí RoleBinding. Při použití v RoleBinding umožňuje přístup pro čtení i zápis ke většině prostředků v oboru názvů, včetně možnosti vytvářet role a vazby rolí v rámci oboru názvů. Tato role nepovoluje přístup pro zápis k kvótě prostředků nebo samotnému oboru názvů. |
| Správce clusteru Azure ARC Kubernetes | Umožňuje přístupu super uživatele ke spuštění jakékoli akce u libovolného prostředku. Při použití v ClusterRoleBinding poskytuje plnou kontrolu nad všemi prostředky v clusteru a ve všech oborech názvů. Při použití v RoleBinding poskytuje plnou kontrolu nad všemi prostředky v oboru názvů vazby role, včetně oboru názvů samotného.|

Přiřazení rolí můžete vytvořit v oboru Kubernetes clusteru s podporou ARC v okně `Access Control (IAM)` prostředku clusteru na Azure Portal. Můžete také použít příkazy rozhraní příkazového řádku Azure, jak je znázorněno níže:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

kde `AZURE-AD-ENTITY-ID` může být uživatelské jméno (například testuser@mytenant.onmicrosoft.com ) nebo i `appId` objekt instančního objektu.

Tady je další příklad vytvoření přiřazení role v oboru pro konkrétní obor názvů v rámci clusteru –

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> I když se přiřazení rolí v oboru pro cluster dá vytvořit pomocí Azure Portal nebo rozhraní příkazového řádku, můžou být přiřazení rolí vymezená obory názvů vytvořená jenom pomocí rozhraní příkazového řádku (CLI).

### <a name="custom-roles"></a>Vlastní role

Můžete zvolit vytvoření vlastní definice role pro použití v přiřazeních rolí.

Projděte si následující příklad definice role, který uživateli umožňuje číst jenom nasazení. Další informace najdete [v úplném seznamu datových akcí, které můžete použít k vytvoření definice role](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Zkopírujte níže uvedený objekt JSON do souboru s názvem custom-role.js. `<subscription-id>`Zástupný text nahraďte skutečným ID předplatného. Níže uvedená vlastní role používá jednu z datových akcí a umožňuje zobrazit všechna nasazení v oboru (cluster/obor názvů), kde je přiřazení role vytvořené.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Vytvořte definici role spuštěním příkazu níže ze složky, kam jste uložili `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Vytvořte přiřazení role pomocí této definice vlastní role:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Konfigurace kubectl s přihlašovacími údaji uživatele

Existují dva způsoby, jak získat `kubeconfig` soubor potřebný pro přístup ke clusteru:
1. Použijte funkci [připojení clusteru](cluster-connect.md) ( `az connectedk8s proxy` ) clusteru Kubernetes s povoleným ARC Azure.
1. Správce clusteru sdílí `kubeconfig` soubor se všemi ostatními uživateli.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Pokud přistupujete ke clusteru pomocí funkce cluster Connect

Spusťte následující příkaz, který spustí proces proxy:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Po spuštění procesu proxy můžete v konzole otevřít jinou kartu a [zahájit odesílání požadavků do clusteru](#sending-requests-to-cluster).

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Pokud `kubeconfig` se soubor s vámi nasdílel správcem clusteru 

1. Spusťte následující příkaz pro nastavení přihlašovacích údajů uživatele:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Otevřete `kubeconfig` soubor, který jste vytvořili dříve. V části `contexts` Ověřte, že kontext přidružený ke clusteru odkazuje na přihlašovací údaje uživatele vytvořené v předchozím kroku.

1. Přidat nastavení **konfiguračního režimu** v části Konfigurace uživatele:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>Odesílání požadavků do clusteru

1. Spusťte libovolný `kubectl` příkaz. Například:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Po zobrazení výzvy k ověření pomocí prohlížeče zkopírujte adresu URL pro přihlášení k zařízení `https://microsoft.com/devicelogin` a otevřete ji ve webovém prohlížeči.

1. Zadejte kód vytištěný v konzole, zkopírujte a vložte kód do terminálu do vstupní výzvy pro ověření zařízení.

1. Zadejte uživatelské jméno ( testuser@mytenant.onmicrosoft.com ) a přidružené heslo.

1. Pokud se vám zobrazí chybová zpráva, znamená to, že nemáte oprávnění k přístupu k požadovanému prostředku:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Správce musí vytvořit přiřazení nové role povolující tomuto uživateli přístup k tomuto prostředku.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> Zabezpečené připojení ke clusteru pomocí [připojení clusteru](cluster-connect.md)