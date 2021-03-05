---
title: Integrace služby Azure Key Vault s protokolem Kubernetes
description: V tomto kurzu získáte přístup k tajným klíčům z trezoru klíčů Azure a načítáte z něj tajné klíče pro připojení do Kubernetes lusků pomocí ovladače rozhraní úložiště kontejnerů (CSI).
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: fd40ad41bda50d93943a514cd9cc3aeeab4ac948
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198876"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Kurz: konfigurace a spuštění poskytovatele Azure Key Vault pro ovladač tajných klíčů úložiště pro Kubernetes

> [!IMPORTANT]
> Ovladač CSI úložiště tajných klíčů je open source projekt, který nepodporuje technická podpora Azure. Ohlaste prosím veškerou zpětnou vazbu a problémy související s ovladačem CSI Key Vault integrací na odkaz na GitHub v dolní části stránky. Tento nástroj je k dispozici uživatelům, kteří si můžou do clusterů sami nainstalovat a shromažďovat názory z naší komunity.

V tomto kurzu získáte přístup k tajným klíčům a jejich načítání z trezoru klíčů Azure pomocí ovladače rozhraní kontejnerů úložiště klíčů (CSI) pro připojení tajných kódů do Kubernetes lusků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Používejte spravované identity.
> * Nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure
> * Nainstalujte Helm. ovladač pro rozhraní CSI uchovává a Azure Key Vault poskytovatele.
> * Vytvořte Trezor klíčů Azure a nastavte své tajné kódy.
> * Vytvořte vlastní objekt SecretProviderClass.
> * Nasaďte svůj pod s připojenými tajnými kódy z trezoru klíčů.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Než začnete s tímto kurzem, nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli-windows).

V tomto kurzu se předpokládá, že používáte službu Azure Kubernetes v uzlech systému Linux.

## <a name="use-managed-identities"></a>Použití spravovaných identit

Tento diagram znázorňuje integrační tok AKS – Key Vault pro spravovanou identitu:

![Diagram, který zobrazuje integrační tok AKS – Key Vault pro spravovanou identitu](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure

Nemusíte používat Azure Cloud Shell. Na příkazovém řádku (terminálu) s nainstalovaným rozhraním Azure CLI stačí. 

Dokončete části Vytvoření skupiny prostředků, vytvoření clusteru AKS a připojení ke clusteru v tématu [nasazení clusteru služby Azure Kubernetes pomocí rozhraní příkazového řádku Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Pokud plánujete použití pod identitou, doporučuje se použít modul plug-in Doporučené sítě `azure` . Další podrobnosti najdete v [dokumentu](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) . Vytvořte cluster Kubernetes, jak je znázorněno v následujícím příkazu:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Nastavte proměnnou prostředí PATH](https://www.java.com/en/download/help/path.xml) na soubor *kubectl.exe* , který jste stáhli.
2. Pomocí následujícího příkazu, který vypíše verzi klienta a serveru, se podívejte na verzi Kubernetes. Verze klienta je *kubectl.exe* soubor, který jste nainstalovali, a verze serveru je služba Azure Kubernetes Services (AKS), na které je váš cluster spuštěný.
    ```azurecli
    kubectl version
    ```
3. Ujistěte se, že verze Kubernetes je 1.16.0 nebo novější. V případě clusterů Windows se ujistěte, že verze Kubernetes je 1.18.0 nebo novější. Následující příkaz upgraduje cluster Kubernetes i fond uzlů. Spuštění příkazu může trvat několik minut. V tomto příkladu je skupina prostředků *contosoResourceGroup* a cluster Kubernetes je *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. Pokud chcete zobrazit metadata clusteru AKS, který jste vytvořili, použijte následující příkaz. Zkopírujte **principalId**, **ClientID**, **SubscriptionId** a **nodeResourceGroup** pro pozdější použití. Pokud se AKS cluster nevytvořil s povolenými spravovanými identitami, bude mít **principalId** a **ClientID** hodnotu null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Výstup zobrazuje jak zvýrazněné parametry:
    
    ![Snímek obrazovky s hodnotami principalId a clientId v rozhraní příkazového řádku Azure se zvýrazněným snímkem pro rozhraní příkazového ](../media/kubernetes-key-vault-2.png) ![ řádku Azure CLI s zvýrazněnými hodnotami SubscriptionId a nodeResourceGroup](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Instalace Helm a ovladače pro uložení tajných kódů
> [!NOTE]
> Níže uvedená instalace funguje pouze na AKS v systému Linux. Další informace o instalaci ovladačů do úložiště tajných klíčů najdete v tématu [ovladače Azure Key Vault Provider for tajných klíčů úložiště CSI](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/) . 

Chcete-li nainstalovat ovladače a poskytovatele Azure Key Vault do úložiště tajných klíčů, musíte nejdřív nainstalovat [Helm](https://helm.sh/docs/intro/install/).

Pomocí rozhraní ovladače pro [úložiště tajných klíčů](https://azure.github.io/secrets-store-csi-driver-provider-azure/) můžete získat tajné kódy, které jsou uložené ve vaší instanci Azure Key Vault, a pak pomocí rozhraní ovladače připojit tajný obsah do lusků Kubernetes.

1. Ověřte, že verze Helm je V3 nebo novější:
    ```azurecli
    helm version
    ```
1. Nainstalujte ovladač s tajnými kódy do úložiště a poskytovatele Azure Key Vault pro ovladač:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Pokud máte v úmyslu používat v uzlech Windows ovladač v tajných tajných klíčích a poskytovatele Azure Key Vault, povolte v uzlech Windows ovladače a zprostředkovatele pomocí [hodnot konfigurace Helm](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration) .

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Vytvoření trezoru klíčů Azure a nastavení tajných kódů

Pokud chcete vytvořit vlastní Trezor klíčů a nastavit tajné kódy, postupujte podle pokynů v části [nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](../secrets/quick-create-cli.md).

> [!NOTE] 
> Nemusíte používat Azure Cloud Shell ani vytvářet novou skupinu prostředků. Můžete použít skupinu prostředků, kterou jste vytvořili dříve pro cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Vytvoření vlastního objektu SecretProviderClass

Chcete-li vytvořit vlastní objekt SecretProviderClass s parametry specifickými pro konkrétního zprostředkovatele pro ovladač pro úložiště tajných klíčů, [použijte tuto šablonu](https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/examples/pod-identity/v1alpha1_secretproviderclass_pod_identity.yaml). Tento objekt zajistí přístup k identitě vašeho trezoru klíčů.

V ukázkovém souboru SecretProviderClass YAML vyplňte chybějící parametry. Jsou vyžadovány následující parametry:

* název **trezoru klíčů: název** vašeho trezoru klíčů
* **objekty**: seznam všech tajných obsahu, který chcete připojit.
    * **ObjectName**: název tajného obsahu
    * **ObjectType**: typ objektu (tajný kód, klíč, certifikát)
* **tenantID**: ID klienta nebo ID adresáře vašeho trezoru klíčů

Dokumentace pro všechna povinná pole a podporované konfigurace je k dispozici zde: [odkaz](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

Aktualizovaná šablona je uvedena v následujícím kódu. Stáhněte si ho jako soubor YAML a vyplňte požadovaná pole. V tomto příkladu je Trezor klíčů **contosoKeyVault5**. Má dva tajné klíče, **secret1** a **secret2**.

> [!NOTE] 
> Pokud používáte identity pod, nastavte hodnotu **usePodIdentity** na *true* a jako dvojici uvozovek (**""**) nastavte hodnotu **userAssignedIdentityID** . Pokud používáte spravované identity, nastavte hodnotu **useVMManagedIdentity** na *true* a jako hodnotu **userAssignedIdentityID** nastavte na clientID uživatelsky přiřazené identity.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
Následující obrázek ukazuje výstup konzoly pro příkaz **AZ datatrezor show--Name contosoKeyVault5** s relevantními zvýrazněnými metadaty:

![Snímek obrazovky s výstupem konzoly pro příkaz AZ webtrezor show--Name contosoKeyVault5](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Nainstalovat Azure Active Directory (Azure AD) pod identitou

1. Přiřaďte ke clusteru AKS, který jste vytvořili, konkrétní role. 

    Dokumentace pro všechna požadovaná přiřazení rolí s Azure Active Directory (Azure AD) pod identitou je dostupná tady: [odkaz](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. Nainstalujte do AKS identitu služby Azure Active Directory (Azure AD).

    > [!NOTE] 
    > Pokud používáte cluster AKS s modulem plug-in kubenet Network, přečtěte si tento [dokument](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) o tom, jak nasadit identitu pod v clusteru.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. Vytvořte User-Assigned spravovanou identitu. Ve výstupu zkopírujte **ClientID** pro pozdější použití.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Udělte identitám oprávnění k získání tajných kódů z trezoru klíčů. Použijte **ClientID** ze spravované identity přiřazené uživatelem.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Nasazení zařízení s připojenými tajnými kódy z trezoru klíčů

Pokud chcete nakonfigurovat objekt SecretProviderClass, spusťte následující příkaz:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Použití spravovaných identit

Pokud používáte identity pod, vytvořte v clusteru *AzureIdentity* , který odkazuje na identitu, kterou jste vytvořili dříve. Pak vytvořte *AzureIdentityBinding* , který odkazuje na AzureIdentity, který jste vytvořili. Vyplňte parametry v následující šabloně a pak ji uložte jako *podIdentityAndBinding. yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Spusťte následující příkaz pro konfiguraci vazby:

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
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
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
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Chcete-li zobrazit obsah konkrétního tajného klíče, spusťte následující příkaz:
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Ověřte, zda se zobrazuje obsah tajného klíče.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak zajistit, aby se váš Trezor klíčů mohl obnovit, najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Zapnout obnovitelné odstranění](./key-vault-recovery.md)
