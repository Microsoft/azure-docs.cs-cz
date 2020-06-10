---
title: Integrace Azure Key Vault s Kubernetes
description: V tomto kurzu budete mít přístup k tajným klíčům z Azure Key Vault a budete je moct načíst pomocí ovladače pro úložiště v angličtině (rozhraní kontejneru úložiště) a pak se připojit k Kubernetes luskům.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637166"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Kurz: konfigurace a spuštění poskytovatele Azure Key Vault pro úložiště tajného úložiště v Kubernetes

V tomto kurzu budete mít přístup k tajným klíčům z Azure Key Vault a budete je moct načíst pomocí ovladače pro úložiště v angličtině (rozhraní kontejneru úložiště) a pak se připojit k Kubernetes luskům.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření instančního objektu
> * Nasazení clusteru služby Azure Kubernetes
> * Nainstalovat ovladač Helm pro ukládání tajných klíčů
> * Vytvoření Azure Key Vault a nastavení tajných kódů
> * Vytvoření vlastního objektu SecretProviderClass
> * Nasaďte svůj pod s připojenými tajnými kódy z Key Vault

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

Než začnete s tímto kurzem, nainstalujte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Vytvoření instančního objektu nebo použití spravovaných identit

Pokud máte v úmyslu používat spravované identity, můžete přejít k další části.

Vytvořte instanční objekt, který bude řídit, k jakým prostředkům bude možné přicházet z Azure Key Vault. Přístup k tomuto instančnímu objektu je omezen rolemi, které jsou jim přiřazeny. Tato funkce poskytuje kontrolu nad tím, jak může instanční objekt spravovat vaše tajné kódy. V následujícím příkladu se název instančního objektu **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Tato operace vrátí řadu párů klíč/hodnota:

![Image](../media/kubernetes-key-vault-1.png)

Zkopírujte **AppID** a **heslo**. Tyto přihlašovací údaje budete potřebovat později.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Nasazení clusteru služby Azure Kubernetes pomocí Azure CLI

Nemusíte používat Azure Cloud Shell, na příkazovém řádku (terminálu) s nainstalovaným rozhraním Azure CLI dojde. 

Postupujte podle tohoto [Průvodce](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) a dokončete následující části: **vytvořte skupinu prostředků**, **vytvořte cluster AKS**a **Připojte se ke clusteru**.

**Poznámka:** Pokud plánujete použít identitu pod, místo instančního objektu. Nezapomeňte ho povolit při vytváření clusteru Kubernetes, jak je znázorněno níže:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Nastavte proměnnou prostředí PATH](https://www.java.com/en/download/help/path.xml) na soubor "kubectl. exe", který se stáhl.
1. Pomocí níže uvedeného příkazu ověřte verzi Kubernetes. Tento příkaz zobrazí výstup verze klienta a serveru. Verze klienta je "kubectl. exe", který jste nainstalovali, když je serverovou verzí služby Azure Kubernetes, na kterých je cluster spuštěný.
    ```azurecli
    kubectl version
    ```
1. Zajistěte, aby byla verze Kubernetes buď **v 1.16.0** , nebo vyšší. Tento příkaz provede upgrade clusteru Kubernetes i fondu uzlů. Spuštění může trvat několik minut. V tomto příkladu je skupina prostředků **contosoResourceGroup** a cluster Kubernetes je **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Zobrazte metadata clusteru AKS, který jste vytvořili, pomocí příkazu níže. Zkopírujte **principalId**, **ClientID**, **SubscriptionId**a **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Toto je výstup s oběma parametry zvýrazněnými.
    
    ![](../media/kubernetes-key-vault-5.png) ![ Obrázek obrázku](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Nainstalovat ovladač Helm pro ukládání tajných klíčů

Pokud chcete nainstalovat ovladač [Helm](https://helm.sh/docs/intro/install/) pro úložiště tajných klíčů, budete muset nainstalovat.

Rozhraní ovladače pro [úložiště tajných klíčů](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) umožňuje získat obsah, který je uložený v instanci Azure Key Vault, a použít ho k připojení těchto tajných obsahů do Kubernetes lusků.

1. Zkontrolujte verzi Helm a ujistěte se, že je V3 nebo vyšší:
    ```azurecli
    helm version
    ```
1. Nainstalujte ovladač s tajnými kódy do úložiště a poskytovatele Azure Key Vault pro ovladač:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Vytvoření Azure Key Vault a nastavení tajných kódů

Podle tohoto [Průvodce](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) vytvořte vlastní Key Vault a nastavte své tajné kódy.

**Poznámka:** Nemusíte používat Azure Cloud Shell ani vytvořit novou skupinu prostředků. Použití skupiny prostředků vytvořené dříve pro cluster Kubernetes je přesné.

## <a name="create-your-own-secretproviderclass-object"></a>Vytvoření vlastního objektu SecretProviderClass

Pomocí této [šablony](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) můžete vytvořit vlastní objekt SecretProviderClass a zadat parametry specifické pro konkrétního poskytovatele pro daný ovladač pro úložiště tajných klíčů. Tento objekt vám poskytne přístup k identitě pro váš Key Vault.

Pomocí zadaného souboru Sample SecretProviderClass YAML. Chystáte se vyplnit chybějící parametry. Jsou vyžadovány následující parametry:

1.  **userAssignedIdentityID:** ID klienta instančního objektu
1.  **Trezor klíčů:** Název Key Vault
1.  **objekty:** Tento objekt bude obsahovat veškerý tajný obsah, který chcete připojit.
    1.  **ObjectName:** Název tajného obsahu
    1.  **ObjectType:** Typ objektu (tajný kód, klíč, certifikát)
1.  **zdroj dat:** Název skupiny prostředků
1.  **SubscriptionId:** ID předplatného Key Vault
1.  **tenantID:** ID tenanta (tj. ID adresáře) Key Vault

Níže je aktualizovaná šablona, stáhněte ji jako soubor. yaml a vyplňte odpovídající požadovaná pole. V tomto příkladu je Key Vault **contosoKeyVault5** a má dva tajné klíče, **secret1** a **secret2**.

**Poznámka:** Pokud používáte spravované identity, pole **usePodIdentity** musí mít **hodnotu true** a pole **userAssignedIdentityID** nechte pouze v uvozovkách **""**. 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Níže je uveden výstup konzoly pro "AZ klíčů trezor show--Name contosoKeyVault5" s relevantními zvýrazněnými metadaty:

![Image](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Přiřazení instančního objektu nebo použití spravovaných identit

### <a name="using-service-principal"></a>Použití instančního objektu

Pokud používáte instanční objekt. Abyste mohli získat přístup k vašemu Key Vault a načíst tajné kódy, musíte mu udělit oprávnění k vašemu objektu služby. Přiřaďte roli **Čtenář** a udělte instančnímu objektu oprávnění k **získání** tajných kódů z vaší Key Vault, a to provedením následujících kroků:

1. Přiřaďte instanční objekt existujícímu Key Vault. Parametr **$AZURE _CLIENT_ID** je identifikátor **appId** , který jste zkopírovali po vytvoření instančního objektu.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Níže je uveden výstup příkazu: 

    ![Image](../media/kubernetes-key-vault-3.png)

1. Udělte instančnímu objektu oprávnění k získání tajných kódů:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Teď, když jste nakonfigurovali instanční objekt tak, aby měl oprávnění ke čtení tajných kódů z vašeho Key Vault. **_CLIENT_SECRET $Azure** je **heslo** k instančnímu objektu. Přidejte přihlašovací údaje instančního objektu jako tajný kód Kubernetes, který je přístupný ovladači pro úložiště tajných kódů.
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Poznámka:** Pokud se vám zobrazí chyba později při nasazování rozhraní Kubernetes pod o neplatné ID tajného klíče klienta. Můžete mít starší ID tajného klíče klienta, jehož platnost vypršela, nebo resetovat. Pokud chcete tento problém vyřešit, odstraňte tajné kódy tajných klíčů-Store-přihlašovací údaje a vytvořte nový s aktuálním ID tajného klíče klienta. Spuštěním následujícího příkazu odstraňte tajné klíče-Store-přihlašovací údaje:
```azurecli
kubectl delete secrets secrets-store-creds
```

Pokud jste zapomněli ID tajného kódu klienta objektu služby, můžete ho resetovat pomocí následujícího příkazu:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Použití spravovaných identit

Pokud používáte spravované identity, přiřaďte ke clusteru AKS, který jste vytvořili, konkrétní role. 
1. Pokud chcete vytvořit nebo vypsat/načíst spravovanou identitu přiřazenou uživatelem, musí být k vašemu clusteru AKS přiřazená role [Přispěvatel spravované identity](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) . Ujistěte se, že **$ClientID** je v clusteru Kubernetes.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Nainstalujte do AKS identitu služby Azure Active Directory (Azure AD).
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Vytvořte identitu Azure AD. Zkopírujte pole **ClientID** a **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Přiřaďte roli Čtenář k identitě Azure AD, kterou jste právě vytvořili pro svůj Key Vault. Pak Udělte identitě oprávnění k získání tajných kódů z vašeho Key Vault. Chystáte se použít **ClientID** a **PrincipalId** z identity Azure, kterou jste právě vytvořili.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Nasaďte svůj pod s připojenými tajnými kódy z Key Vault

Následující příkaz provede konfiguraci objektu SecretProviderClass:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Použití instančního objektu

Pokud používáte instanční objekt. Níže uvedený příkaz nasadí své Kubernetes lusky s SecretProviderClass a tajnými klíči-Store-přihlašovací údaje, které jste nakonfigurovali. Tady je šablona pro nasazení pro [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) a [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) .
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Použití spravovaných identit

Pokud používáte spravované identity. V clusteru vytvoříte **AzureIdentity** , který bude odkazovat na identitu, kterou jste vytvořili dříve. Pak vytvořte **AzureIdentityBinding** , který bude odkazovat na **AzureIdentity** , který jste vytvořili. Použijte níže uvedenou šablonu, vyplňte odpovídající parametry a uložte je jako **podIdentityAndBinding. yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Spusťte následující příkaz pro provedení vazby:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Další je skutečné nasazení pod. Níže je uvedený soubor YAML nasazení, který použije vazbu identity pod v posledním kroku. Uložte tento soubor jako **podBindingDeployment. yaml**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

Spusťte následující příkaz, který nasadí svůj pod:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>Kontrolovat stav a tajný obsah 
Chcete-li zobrazit lusky, které jste nasadili:
```azurecli
kubectl get pods
```

Chcete-li zjistit stav pod, použijte následující příkaz:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Image](../media/kubernetes-key-vault-4.png)

Nasazený pod by měl být ve stavu spuštěno. V části "události" v dolní části jsou všechny typy událostí vlevo klasifikovány jako "normální".
Jakmile ověříte, že je spuštěný, můžete ověřit, že vaše zařízení má tajná klíč z Key Vault.

Zobrazení všech tajných kódů, které má pod:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Postup získání obsahu z konkrétního tajného klíče:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Ověřte obsah zobrazeného tajného kódu.

## <a name="next-steps"></a>Další kroky

Ujistěte se, že je vaše Key Vault obnovitelné:
> [!div class="nextstepaction"]
> [Zapnout obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
