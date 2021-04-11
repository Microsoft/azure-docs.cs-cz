---
title: Připojení clusteru s povolenými Kubernetes clustery Azure pomocí připojení clusteru
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Připojte se k zabezpečenému připojení ke clusterům Kubernetes s povoleným ARC Azure pomocí připojení clusteru.
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450976"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Připojení clusteru s povolenými Kubernetes clustery Azure pomocí připojení clusteru

S připojením ke clusteru se můžete bezpečně připojit ke clusterům Kubernetes s povoleným ARC Azure bez nutnosti povolit v bráně firewall žádný port pro příchozí spojení. Přístup k `apiserver` Kubernetes clusteru s povoleným ARC umožňuje následující scénáře:
* Povolte interaktivní ladění a řešení potíží.
* Poskytněte přístup clusteru ke službám Azure pro [vlastní umístění](custom-locations.md) a další prostředky, které se na něm vytvořily.

Koncepční přehled této funkce je k dispozici v článku o [připojení ke službě cluster Connect – Azure ARC Enabled v Kubernetes](conceptual-cluster-connect.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Požadavky   

- [Instalace nebo upgrade rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na verzi >= 2.16.0

- Nainstalujte `connectedk8s` rozšíření Azure CLI verze >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Pokud jste již `connectedk8s` rozšíření nainstalovali, aktualizujte rozšíření na nejnovější verzi:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Existující Kubernetes připojený cluster s podporou Azure ARC.
    - Pokud jste ještě nepřipojili cluster, Využijte náš [rychlý Start](quickstart-connect-cluster.md).
    - [Upgradujte agenty](agent-upgrade.md#manually-upgrade-agents) na verzi >= 1.1.0.

- Povolte cluster připojit na jakémkoli clusteru Kubernetes s povoleným obloukem Azure pomocí následujícího příkazu na počítači, na kterém `kubeconfig` je tento soubor odkazován na cluster, ke kterému se vztahuje.

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Kromě těch, které jsou uvedené v části [připojení clusteru Kubernetes ke službě Azure ARC](quickstart-connect-cluster.md#meet-network-requirements), povolte níže uvedené koncové body pro odchozí přístup:

    | Koncový bod | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Využití

Funkce připojení clusteru podporuje dvě možnosti ověřování: 
* Azure Active Directory (Azure AD) 
* Token účtu služby

### <a name="option-1-azure-active-directory"></a>Možnost 1: Azure Active Directory

1. Pomocí `kubeconfig` souboru, který odkazuje na `apiserver` váš cluster Kubernetes, vytvořte ClusterRoleBinding nebo RoleBinding entitu služby Azure AD (instanční objekt nebo uživatel), který vyžaduje přístup:

    **Pro uživatele:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Pro aplikaci Azure AD:**

    1. Získejte `objectId` přidružený k aplikaci Azure AD:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Vytvořte ClusterRoleBinding nebo RoleBinding k entitě služby Azure AD (instanční objekt nebo uživatel), která potřebuje přístup k tomuto clusteru:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Až se přihlásíte do Azure CLI pomocí entity Azure AD, připojte se ke clusteru, `kubeconfig` který je potřeba ke komunikaci s clusterem, odkudkoli (přes bránu firewall, která tento cluster obklopuje):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Použijte `kubectl` k odeslání požadavků do clusteru:

    ```console
    kubectl get pods
    ```
    
    Nyní by se měla zobrazit odpověď z clusteru, který obsahuje seznam všech lusků pod `default` oborem názvů.

### <a name="option-2-service-account-bearer-token"></a>Možnost 2: token nosiče účtu služby

1. Se `kubeconfig` souborem, který odkazuje na `apiserver` váš cluster Kubernetes, vytvořte účet služby v jakémkoli oboru názvů (následující příkaz ho vytvoří ve výchozím oboru názvů):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Vytvořte ClusterRoleBinding nebo RoleBinding, abyste tomuto [účtu služby udělili příslušná oprávnění ke clusteru](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding):

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Získejte token účtu služby pomocí následujících příkazů.

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Získejte, aby byl cluster připojen `kubeconfig` ke komunikaci s clusterem odkudkoli (od jiných než přes bránu firewall, která ho obklopuje v clusteru):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Použijte `kubectl` k odeslání požadavků do clusteru:

    ```console
    kubectl get pods
    ```

    Nyní by se měla zobrazit odpověď z clusteru, který obsahuje seznam všech lusků pod `default` oborem názvů.

## <a name="known-limitations"></a>Známá omezení

Pokud se při vytváření požadavků do clusteru Kubernetes nachází entita služby Azure AD součástí více než 200 skupin, dojde k následující chybě, protože se jedná o známé omezení:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Pokud se chcete dostat k této chybě:
1. Vytvoření [instančního objektu](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli), který může být méně pravděpodobný členem více než 200 skupin.
1. Před spuštěním příkazu se [přihlaste](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) ke službě Azure CLI pomocí instančního objektu `az connectedk8s proxy` .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> Nastavení [služby Azure AD RBAC](azure-rbac.md) na svých clusterech