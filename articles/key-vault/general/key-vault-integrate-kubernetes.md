---
title: Integrace služby Azure Key Vault s protokolem Kubernetes
description: V tomto kurzu získáte přístup k tajným klíčům z trezoru klíčů Azure a načítáte z něj tajné klíče pro připojení do Kubernetes lusků pomocí ovladače rozhraní úložiště kontejnerů (CSI).
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: f4981036ca92f6efe2d3e23ea1f507a3a1f3c70a
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234252"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Kurz: konfigurace a spuštění poskytovatele Azure Key Vault pro ovladač tajných klíčů úložiště pro Kubernetes

> [!IMPORTANT]
> Ovladač CSI je open source projekt, který není podporován technickou podporou Azure. Ohlaste prosím veškerou zpětnou vazbu a problémy související s ovladačem CSI Key Vault integrací na GitHubu [.](https://github.com/Azure/secrets-store-csi-driver-provider-azure/issues) Tento nástroj je k dispozici uživatelům, kteří si můžou do clusterů sami nainstalovat a shromažďovat názory z naší komunity.


V tomto kurzu získáte přístup k tajným klíčům a jejich načítání z trezoru klíčů Azure pomocí ovladače rozhraní kontejnerů úložiště klíčů (CSI) pro připojení tajných kódů do Kubernetes lusků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Používejte spravované identity.
> * Nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure
> * Nainstalujte Helm a ovladač CSI pro úložiště tajných kódů.
> * Vytvořte Trezor klíčů Azure a nastavte své tajné kódy.
> * Vytvořte vlastní objekt SecretProviderClass.
> * Nasaďte svůj pod s připojenými tajnými kódy z trezoru klíčů.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Než začnete s tímto kurzem, nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

V tomto kurzu se předpokládá, že runnig službu Azure Kubernetes v uzlech systému Linux.

## <a name="use-managed-identities"></a>Použití spravovaných identit

Tento diagram znázorňuje integrační tok AKS – Key Vault pro spravovanou identitu:

![Diagram, který zobrazuje integrační tok AKS – Key Vault pro spravovanou identitu](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure

Nemusíte používat Azure Cloud Shell. Na příkazovém řádku (terminálu) s nainstalovaným rozhraním Azure CLI stačí. 

Dokončete části Vytvoření skupiny prostředků, vytvoření clusteru AKS a připojení ke clusteru v tématu [nasazení clusteru služby Azure Kubernetes pomocí rozhraní příkazového řádku Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Pokud plánujete použít identitu pod, nezapomeňte ji povolit při vytváření clusteru Kubernetes, jak je znázorněno v následujícím příkazu:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Nastavte proměnnou prostředí PATH](https://www.java.com/en/download/help/path.xml) na soubor *kubectl.exe* , který jste stáhli.
1. Pomocí následujícího příkazu, který vypíše verzi klienta a serveru, se podívejte na verzi Kubernetes. Verze klienta je *kubectl.exe* soubor, který jste nainstalovali, a verze serveru je služba Azure Kubernetes Services (AKS), na které je váš cluster spuštěný.
    ```azurecli
    kubectl version
    ```
1. Ujistěte se, že verze Kubernetes je 1.16.0 nebo novější. V případě clusterů Windows se ujistěte, že verze Kubernetes je 1.18.0 nebo novější. Následující příkaz upgraduje cluster Kubernetes i fond uzlů. Spuštění příkazu může trvat několik minut. V tomto příkladu je skupina prostředků *contosoResourceGroup* a cluster Kubernetes je *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Pokud chcete zobrazit metadata clusteru AKS, který jste vytvořili, použijte následující příkaz. Zkopírujte **principalId**, **ClientID**, **SubscriptionId** a **nodeResourceGroup** pro pozdější použití. Pokud se AKS cluster nevytvořil s povolenými spravovanými identitami, bude mít **principalId** a **ClientID** hodnotu null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Výstup zobrazuje jak zvýrazněné parametry:
    
    ![Snímek obrazovky s hodnotami principalId a clientId v rozhraní příkazového řádku Azure se zvýrazněným snímkem pro rozhraní příkazového ](../media/kubernetes-key-vault-2.png) ![ řádku Azure CLI s zvýrazněnými hodnotami SubscriptionId a nodeResourceGroup](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Instalace Helm a ovladače pro uložení tajných kódů
> [!NOTE]
> Níže uvedená instalace funguje pouze na AKS v systému Linux. Další informace o instalaci ovladačů do úložiště tajných klíčů najdete v tématu [ovladače Azure Key Vault Provider for tajných klíčů úložiště CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure) . 

K instalaci ovladače pro ukládání tajných klíčů si musíte nejdřív nainstalovat [Helm](https://helm.sh/docs/intro/install/).

Pomocí rozhraní ovladače pro [úložiště tajných klíčů](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) můžete získat tajné kódy, které jsou uložené v instanci trezoru klíčů Azure, a pak pomocí rozhraní ovladače připojit tajný obsah do Kubernetes lusků.

1. Ověřte, že verze Helm je V3 nebo novější:
    ```azurecli
    helm version
    ```
1. Nainstalujte ovladač s tajnými kódy do úložiště a poskytovatele Azure Key Vault pro ovladač:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Vytvoření trezoru klíčů Azure a nastavení tajných kódů

Pokud chcete vytvořit vlastní Trezor klíčů a nastavit tajné kódy, postupujte podle pokynů v části [nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Nemusíte používat Azure Cloud Shell ani vytvářet novou skupinu prostředků. Můžete použít skupinu prostředků, kterou jste vytvořili dříve pro cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Vytvoření vlastního objektu SecretProviderClass

Chcete-li vytvořit vlastní objekt SecretProviderClass s parametry specifickými pro konkrétního zprostředkovatele pro ovladač pro úložiště tajných klíčů, [použijte tuto šablonu](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml). Tento objekt zajistí přístup k identitě vašeho trezoru klíčů.

V ukázkovém souboru SecretProviderClass YAML vyplňte chybějící parametry. Jsou vyžadovány následující parametry:

* **userAssignedIdentityID**: # [povinné] Pokud je hodnota prázdná, použije se ve výchozím nastavení identita přiřazená systémem na virtuálním počítači. 
* název **trezoru klíčů: název** vašeho trezoru klíčů
* **objekty**: kontejner pro všechen tajný obsah, který chcete připojit.
    * **ObjectName**: název tajného obsahu
    * **ObjectType**: typ objektu (tajný kód, klíč, certifikát)
* skupina **prostředků: název** skupiny prostředků # [požadováno pro Version < 0.0.4] skupiny prostředků trezoru klíčů.
* **SubscriptionId**: ID předplatného vašeho trezoru klíčů # [požadováno pro Version < 0.0.4] ID předplatného trezoru klíčů.
* **tenantID**: ID klienta nebo ID adresáře vašeho trezoru klíčů

Dokumentace všech povinných polí je k dispozici zde: [odkaz](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

Aktualizovaná šablona je uvedena v následujícím kódu. Stáhněte si ho jako soubor YAML a vyplňte požadovaná pole. V tomto příkladu je Trezor klíčů **contosoKeyVault5**. Má dva tajné klíče, **secret1** a **secret2**.

> [!NOTE] 
> Pokud používáte spravované identity, nastavte hodnotu **usePodIdentity** na *true* a jako dvojici uvozovek (**""**) nastavte hodnotu **userAssignedIdentityID** . 

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
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED]  If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                         
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
Následující obrázek ukazuje výstup konzoly pro příkaz **AZ datatrezor show--Name contosoKeyVault5** s relevantními zvýrazněnými metadaty:

![Snímek obrazovky s výstupem konzoly pro příkaz AZ webtrezor show--Name contosoKeyVault5](../media/kubernetes-key-vault-4.png)

## <a name="assign-managed-identity"></a>Přiřazení spravované identity

Přiřaďte ke clusteru AKS, který jste vytvořili, konkrétní role. 

1. Pokud chcete vytvořit, vypsat nebo načíst spravovanou identitu přiřazenou uživatelem, musí mít váš cluster AKS přiřazenou roli [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Ujistěte se, že **$ClientID** je ClientID clusteru Kubernetes. V případě tohoto oboru se bude jednat o službu předplatného Azure, konkrétně skupinu prostředků uzlu, která byla vytvořena při vytvoření clusteru AKS. Tento rozsah zajistí, že role přiřazené níže mají vliv jenom na prostředky v této skupině. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

1. Nainstalujte do AKS identitu služby Azure Active Directory (Azure AD).
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Vytvořte identitu Azure AD. Ve výstupu zkopírujte **ClientID** a **principalId** pro pozdější použití.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Přiřaďte roli *Čtenář* k identitě Azure AD, kterou jste vytvořili v předchozím kroku vašeho trezoru klíčů, a pak udělte oprávnění identit k získání tajných kódů z trezoru klíčů. Použijte **ClientID** a **PrincipalId** z identity Azure AD.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/<SUBID>/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Nasazení zařízení s připojenými tajnými kódy z trezoru klíčů

Pokud chcete nakonfigurovat objekt SecretProviderClass, spusťte následující příkaz:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Použití spravovaných identit

Pokud používáte spravované identity, vytvořte v clusteru *AzureIdentity* , který odkazuje na identitu, kterou jste vytvořili dříve. Pak vytvořte *AzureIdentityBinding* , který odkazuje na AzureIdentity, který jste vytvořili. Vyplňte parametry v následující šabloně a pak ji uložte jako *podIdentityAndBinding. yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Spusťte následující příkaz pro provedení vazby:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Dále nasadíte pod. Následující kód je soubor YAML nasazení, který používá vazbu identity pod předchozím krokem. Uložte tento soubor jako *podBindingDeployment. yaml*.

```yml
apiVersion: v1
kind: Pod
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

### <a name="check-the-pod-status-and-secret-content"></a>Podívejte se na stav pod a tajný obsah. 

Chcete-li zobrazit lusky, které jste nasadili, spusťte následující příkaz:
```azurecli
kubectl get pods
```

Chcete-li zjistit stav pod, spusťte následující příkaz:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Snímek obrazovky výstupu rozhraní příkazového řádku Azure, který zobrazuje stav spuštěno a zobrazuje všechny události jako "normální" ](../media/kubernetes-key-vault-6.png)

V okně výstup by měl být nasazený pod v *běžícím* stavu. V části **events (události** ) v dolní části jsou všechny typy událostí zobrazeny jako *normální*.

Po ověření, že je pod tím spuštěná možnost pod, můžete ověřit, že v poli obsahuje tajné kódy z trezoru klíčů.

Chcete-li zobrazit všechny tajné kódy, které jsou obsaženy v části pod, spusťte následující příkaz:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Chcete-li zobrazit obsah konkrétního tajného klíče, spusťte následující příkaz:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Ověřte, zda se zobrazuje obsah tajného klíče.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak zajistit, aby se váš Trezor klíčů mohl obnovit, najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Zapnout obnovitelné odstranění](./key-vault-recovery.md)
