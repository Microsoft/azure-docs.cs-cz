---
title: Použití identity AAD ve webové službě
titleSuffix: Azure Machine Learning
description: Pomocí identity AAD s webovou službou ve službě Azure Kubernetes Service můžete během vyhodnocování přistupovat ke cloudovým prostředkům.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122841"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Použití identity Azure AD s webovou službou strojového učení ve službě Azure Kubernetes

V tomto návodu se dozvíte, jak přiřadit identitu Služby Azure Active Directory (AAD) k vašemu nasazenému modelu strojového učení ve službě Azure Kubernetes. Projekt [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) umožňuje aplikacím bezpečný přístup ke cloudovým prostředkům pomocí služby AAD pomocí [primitiv spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) a kubernetes. To umožňuje webové službě zabezpečit přístup k prostředkům Azure bez nutnosti vkládat přihlašovací údaje nebo spravovat tokeny přímo do skriptu. `score.py` Tento článek vysvětluje kroky k vytvoření a instalaci azure identity v clusteru služby Azure Kubernetes a přiřazení identity k nasazené webové službě.

## <a name="prerequisites"></a>Požadavky

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), Azure Machine Learning [SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo rozšíření Azure Machine Learning Visual Studio [Code](tutorial-setup-vscode-extension.md).

- Přístup ke clusteru AKS pomocí příkazu. `kubectl` Další informace naleznete v [tématu Připojení ke clusteru](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Webová služba Azure Machine Learning nasazená do vašeho clusteru AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Vytvoření a instalace Azure Identity v clusteru AKS

1. Chcete-li zjistit, zda je váš cluster AKS povolen RBAC, použijte následující příkaz:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Tento příkaz vrátí `true` hodnotu, pokud je povolenrbac. Tato hodnota určuje příkaz, který má být používán v dalším kroku.

1. Chcete-li nainstalovat [identitu podu AAD](https://github.com/Azure/aad-pod-identity#getting-started) do clusteru AKS, použijte jeden z následujících příkazů:

    * Pokud je v clusteru AKS **povolen systém RBAC,** použijte následující příkaz:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Pokud váš cluster AKS **nemá povolený rbac**, použijte následující příkaz:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        Výstup příkazu je podobný následujícímu textu:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Vytvořte identitu Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) podle kroků uvedených na stránce projektu AAD Pod Identity.

1. [Nainstalujte Azure Identity](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) podle kroků uvedených na stránce projektu AAD Pod Identity.

1. [Nainstalujte vazbu identity Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) podle kroků uvedených na stránce projektu AAD Pod Identity.

1. Pokud identita Azure vytvořená v předchozím kroku není ve stejné skupině prostředků jako váš cluster AKS, postupujte podle [pokynů uvedených](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) na stránce projektu AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Přiřazení Azure Identity k webové službě strojového učení

Následující kroky používají Identitu Azure vytvořenou v předchozí části a přiřaďte ji webové službě AKS prostřednictvím **štítku voliče**.

Nejprve určete název a obor názvů vašeho nasazení v clusteru AKS, který chcete přiřadit Identity Azure. Tyto informace můžete získat spuštěním následujícího příkazu. Obory názvů by měly být název pracovního prostoru Azure Machine Learning a název vašeho nasazení by měl být název koncového bodu, jak je znázorněno na portálu.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Přidejte do nasazení popisek voliče Identity Azure úpravou specifikace nasazení. Hodnota voliče by měla být ta, kterou jste definovali v kroku 5 [instalace vazby identity Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Upravte nasazení a přidejte popisek voliče identity Azure. Přejděte do následující `/spec/template/metadata/labels`části v části . Měli byste vidět `isazuremlapp: “true”`hodnoty, jako je například . Přidejte popisek identity aad-pod-identity, jak je uvedeno níže.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Chcete-li ověřit, zda byl popisek správně přidán, spusťte následující příkaz.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Chcete-li zobrazit všechny stavy podu, spusťte následující příkaz.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Jakmile pody jsou v provozu, webové služby pro toto nasazení teď bude moct přístup k prostředkům Azure prostřednictvím azure identity bez nutnosti vložit přihlašovací údaje do kódu. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Přiřazení příslušných rolí k azure identitě

[Přiřaďte svou spravovanou identitu Azure s příslušnými rolemi](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) pro přístup k dalším prostředkům Azure. Ujistěte se, že role, které přiřazujete, mají správné **akce dat**. Například [role čtečky dat objektů blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) bude mít oprávnění ke čtení objektu blob úložiště, zatímco obecná role [čtečky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) nemusí.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Používání Azure Identity s webovou službou strojového učení

Nasazení modelu do clusteru AKS. Skript `score.py` může obsahovat operace odkazující na prostředky Azure, ke kterým má vaše Azure Identita přístup. Ujistěte se, že jste nainstalovali požadované závislosti klientské knihovny pro prostředek, ke kterému se pokoušíte získat přístup. Níže je uvedeno několik příkladů, jak můžete pomocí azure identity přistupovat k různým prostředkům Azure z vaší služby.

### <a name="access-key-vault-from-your-web-service"></a>Přístup k trezoru klíčů z webové služby

Pokud jste udělili azure identity přístup pro čtení tajný `score.py` klíč uvnitř **trezoru klíčů**, můžete k němu přistupovat pomocí následujícího kódu.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Přístup k objektu blob z webové služby

Pokud jste dali azure identity přístup ke čtení dat `score.py` v **objektu blob úložiště**, můžete k němu přistupovat pomocí následujícího kódu.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak používat klientskou knihovnu identit Pythonu Azure, najdete v [úložišti](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) na GitHubu.
* Podrobný návod k nasazení modelů do clusterů služby Azure Kubernetes najdete v [tématu návody](how-to-deploy-azure-kubernetes-service.md).