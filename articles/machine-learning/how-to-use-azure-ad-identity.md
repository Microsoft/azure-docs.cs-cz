---
title: Použití identity AAD u webové služby
titleSuffix: Azure Machine Learning
description: K přístupu k prostředkům cloudu během bodování použijte identitu AAD u své webové služby ve službě Azure Kubernetes.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 02/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1789f83f048a2ab0fb75aa33635e58b0850b865b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319124"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Použití identity Azure AD s webovou službou strojového učení ve službě Azure Kubernetes Service

V tomto postupu se dozvíte, jak přiřadit identitu služby Azure Active Directory (AAD) k nasazenému modelu Machine Learning ve službě Azure Kubernetes. Projekt [AAD pod identitou](https://github.com/Azure/aad-pod-identity) umožňuje aplikacím zabezpečeně přistupovat ke cloudovým prostředkům pomocí AAD pomocí [spravovaných identit](../active-directory/managed-identities-azure-resources/overview.md) a Kubernetes primitiv. Díky tomu může vaše webová služba zabezpečeně přistupovat k prostředkům Azure bez nutnosti vkládat přihlašovací údaje nebo spravovat tokeny přímo ve vašem `score.py` skriptu. Tento článek popisuje kroky pro vytvoření a instalaci identity Azure v clusteru služby Azure Kubernetes a přiřazení identity k nasazené webové službě.

## <a name="prerequisites"></a>Předpoklady

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [sadu Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Přístup ke clusteru AKS pomocí `kubectl` příkazu. Další informace najdete v tématu [připojení ke clusteru](../aks/kubernetes-walkthrough.md#connect-to-the-cluster) .

- Azure Machine Learning webová služba nasazená do clusteru AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Vytvoření a instalace identity Azure do clusteru AKS

1. Pokud chcete zjistit, jestli je váš cluster AKS povolený RBAC, použijte následující příkaz:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Tento příkaz vrátí hodnotu, `true` Pokud je povolena funkce RBAC. Tato hodnota určuje příkaz, který se má použít v dalším kroku.

1. Pokud chcete do clusteru AKS nainstalovat [identitu AAD pod](https://github.com/Azure/aad-pod-identity#getting-started) , použijte jeden z následujících příkazů:

    * Pokud má cluster AKS **povolené RBAC** , použijte následující příkaz:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Pokud v clusteru AKS **není povolena funkce RBAC** , použijte následující příkaz:
    
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

1. [Vytvořte identitu Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) podle kroků uvedených na stránce projektu AAD pod identitou.

1. [Identitu Azure nainstalujte](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) podle kroků uvedených na stránce projektu AAD pod identitou.

1. [Nainstalujte službu Azure identity Binding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) podle kroků uvedených na stránce projektu AAD pod identitou.

1. Pokud identita Azure vytvořená v předchozím kroku není ve stejné skupině prostředků jako cluster AKS, postupujte podle kroků uvedených na stránce projektu AAD pod identitou [nastavit oprávnění pro mikrofon](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) .

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Přiřazení identity Azure k webové službě Machine Learning

Následující postup použijte identitu Azure vytvořenou v předchozí části a přiřaďte ji k webové službě AKS prostřednictvím **popisku selektoru**.

Nejdřív v clusteru AKS určete název a obor názvů vašeho nasazení, ke kterému chcete přiřadit identitu Azure. Tyto informace můžete získat spuštěním následujícího příkazu. Obory názvů by měly být Azure Machine Learning název pracovního prostoru a název vašeho nasazení by měl být vaším názvem koncového bodu, jak je znázorněno na portálu.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Přidejte k nasazení popisek Azure identity Selector úpravou specifikace nasazení. Hodnota selektoru by měla být ta, kterou jste definovali v kroku 5 [instalace vazby Azure identity](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Úpravou nasazení přidejte popisek Azure identity Selector. V části použijte následující oddíl `/spec/template/metadata/labels` . Měli byste vidět hodnoty, jako je například `isazuremlapp: “true”` . Přidejte popisek AAD-pod-identity, jak je znázorněno níže.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Chcete-li ověřit, zda byl popisek správně přidán, spusťte následující příkaz.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Pokud chcete zobrazit všechny stavy pod, spusťte následující příkaz.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Až budou lusky v provozu, webové služby pro toto nasazení teď budou mít přístup k prostředkům Azure prostřednictvím vaší identity Azure bez nutnosti vkládat přihlašovací údaje do kódu. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Přiřazení příslušných rolí k identitě Azure

[Přiřaďte spravovanou identitu Azure k příslušným rolím](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) pro přístup k dalším prostředkům Azure. Ujistěte se, že role, které přiřazujete, mají správné **akce s daty**. Například [role čtečky dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) bude mít oprávnění ke čtení pro objekt BLOB úložiště, zatímco [role obecného čtecího modulu](../role-based-access-control/built-in-roles.md#reader) nemusí.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Použití identity Azure s webovou službou Machine Learning

Nasaďte model do clusteru AKS. `score.py`Skript může obsahovat operace ukazující na prostředky Azure, ke kterým má vaše identita Azure přístup. Ujistěte se, že jste nainstalovali požadované závislosti klientské knihovny pro prostředek, ke kterému se pokoušíte získat přístup. Níže najdete několik příkladů použití identity Azure pro přístup k různým prostředkům Azure z vaší služby.

### <a name="access-key-vault-from-your-web-service"></a>Přístup k Key Vault z webové služby

Pokud jste vašemu rámci **Key Vault** udělili přístup ke čtení identity Azure, `score.py` můžete k němu přistupovat pomocí následujícího kódu.

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

> [!IMPORTANT]
> V tomto příkladu se používá DefaultAzureCredential. Pokud chcete přístup k identitě udělit pomocí konkrétní zásady přístupu, přečtěte si téma [přiřazení zásad Key Vault přístupu pomocí Azure CLI](../key-vault/general/assign-access-policy-cli.md).

### <a name="access-blob-from-your-web-service"></a>Přístup k objektu BLOB z webové služby

Pokud jste vašemu identitě Azure udělili přístup pro čtení k datům v **objektu BLOB úložiště** , `score.py` můžete k němu přistupovat pomocí následujícího kódu.

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

* Další informace o tom, jak používat klientskou knihovnu Azure identity v Pythonu, najdete v [úložišti](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) na GitHubu.
* Podrobný průvodce nasazením modelů do clusterů služby Azure Kubernetes najdete v tématu [postupy](how-to-deploy-azure-kubernetes-service.md).