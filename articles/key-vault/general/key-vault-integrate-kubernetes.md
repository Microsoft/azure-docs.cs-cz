---
title: Integrace služby Azure Key Vault s protokolem Kubernetes
description: V tomto kurzu získáte přístup k tajným klíčům z trezoru klíčů Azure a načítáte z něj tajné klíče pro připojení do Kubernetes lusků pomocí ovladače rozhraní úložiště kontejnerů (CSI).
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: c101cb4eca246ee68a30ba3499981c589c564f92
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368651"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Kurz: konfigurace a spuštění poskytovatele Azure Key Vault pro ovladač tajných klíčů úložiště pro Kubernetes

> [!IMPORTANT]
> Ovladač CSI je open source projekt, který není podporován technickou podporou Azure. Ohlaste prosím veškerou zpětnou vazbu a problémy související s ovladačem CSI Key Vault integrací na odkaz na GitHub v dolní části stránky. Tento nástroj je k dispozici uživatelům, kteří si můžou do clusterů sami nainstalovat a shromažďovat názory z naší komunity.

V tomto kurzu získáte přístup k tajným klíčům a jejich načítání z trezoru klíčů Azure pomocí ovladače rozhraní kontejnerů úložiště klíčů (CSI) pro připojení tajných kódů do Kubernetes lusků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte instanční objekt nebo použijte spravované identity.
> * Nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure
> * Nainstalujte Helm a ovladač CSI pro úložiště tajných kódů.
> * Vytvořte Trezor klíčů Azure a nastavte své tajné kódy.
> * Vytvořte vlastní objekt SecretProviderClass.
> * Přiřaďte objekt služby nebo použijte spravované identity.
> * Nasaďte svůj pod s připojenými tajnými kódy z trezoru klíčů.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Než začnete s tímto kurzem, nainstalujte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Vytvoření instančního objektu nebo použití spravovaných identit

Pokud máte v úmyslu používat spravované identity, můžete přejít k další části.

Vytvořte instanční objekt pro řízení prostředků, ke kterým se dá dostat z vašeho trezoru klíčů Azure. Přístup k tomuto instančnímu objektu je omezen rolemi, které jsou jim přiřazeny. Tato funkce poskytuje kontrolu nad tím, jak může instanční objekt spravovat vaše tajné kódy. V následujícím příkladu má název instančního objektu hodnotu *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Tato operace vrátí řadu párů klíč/hodnota:

![Snímek obrazovky zobrazující appId a heslo pro contosoServicePrincipal](../media/kubernetes-key-vault-1.png)

Zkopírujte přihlašovací údaje **appId** a **Password** pro pozdější použití.

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Nasazení clusteru služby Azure Kubernetes (AKS) pomocí rozhraní příkazového řádku Azure

Nemusíte používat Azure Cloud Shell. Na příkazovém řádku (terminálu) s nainstalovaným rozhraním Azure CLI stačí. 

Dokončete části Vytvoření skupiny prostředků, vytvoření clusteru AKS a připojení ke clusteru v tématu [nasazení clusteru služby Azure Kubernetes pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough). 

> [!NOTE] 
> Pokud plánujete místo instančního objektu použít identitu pod, nezapomeňte ji povolit při vytváření clusteru Kubernetes, jak je znázorněno v následujícím příkazu:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Nastavte proměnnou prostředí PATH](https://www.java.com/en/download/help/path.xml) na soubor *kubectl.exe* , který jste stáhli.
1. Pomocí následujícího příkazu, který vypíše verzi klienta a serveru, se podívejte na verzi Kubernetes. Verze klienta je *kubectl.exe* soubor, který jste nainstalovali, a verze serveru je služba Azure Kubernetes Services (AKS), na které je váš cluster spuštěný.
    ```azurecli
    kubectl version
    ```
1. Ujistěte se, že verze Kubernetes je 1.16.0 nebo novější. V případě clusterů Windows se ujistěte, že verze Kubernetes je 1.18.0 nebo novější. Následující příkaz upgraduje cluster Kubernetes i fond uzlů. Spuštění příkazu může trvat několik minut. V tomto příkladu je skupina prostředků *contosoResourceGroup*a cluster Kubernetes je *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Pokud chcete zobrazit metadata clusteru AKS, který jste vytvořili, použijte následující příkaz. Zkopírujte **principalId**, **ClientID**, **SubscriptionId**a **nodeResourceGroup** pro pozdější použití. Pokud se nevytvořil cluster s DOTAZem s povolenými spravovanými identitami, bude mít hodnota **principalId** a **ClientID** hodnotu null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Výstup zobrazuje jak zvýrazněné parametry:
    
    ![Snímek obrazovky s hodnotami principalId a clientId v rozhraní příkazového řádku Azure se zvýrazněným snímkem pro rozhraní příkazového ](../media/kubernetes-key-vault-2.png) ![ řádku Azure CLI s zvýrazněnými hodnotami SubscriptionId a nodeResourceGroup](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Instalace Helm a ovladače pro uložení tajných kódů

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

Pokud chcete vytvořit vlastní Trezor klíčů a nastavit tajné kódy, postupujte podle pokynů v části [nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure CLI](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli).

> [!NOTE] 
> Nemusíte používat Azure Cloud Shell ani vytvářet novou skupinu prostředků. Můžete použít skupinu prostředků, kterou jste vytvořili dříve pro cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Vytvoření vlastního objektu SecretProviderClass

Chcete-li vytvořit vlastní objekt SecretProviderClass s parametry specifickými pro konkrétního zprostředkovatele pro ovladač pro úložiště tajných klíčů, [použijte tuto šablonu](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml). Tento objekt zajistí přístup k identitě vašeho trezoru klíčů.

V ukázkovém souboru SecretProviderClass YAML vyplňte chybějící parametry. Jsou vyžadovány následující parametry:

* **userAssignedIdentityID**: # [povinné] Pokud používáte instanční objekt, pomocí ID klienta určete, která spravovaná identita přiřazená uživatelem se má použít. Pokud jako spravovanou identitu virtuálního počítače používáte identitu přiřazenou uživatelem, zadejte ID klienta identity. Pokud je hodnota prázdná, použije se ve výchozím nastavení identita přiřazená systémem na virtuálním počítači. 
* název **trezoru klíčů: název**vašeho trezoru klíčů
* **objekty**: kontejner pro všechen tajný obsah, který chcete připojit.
    * **ObjectName**: název tajného obsahu
    * **ObjectType**: typ objektu (tajný kód, klíč, certifikát)
* skupina **prostředků: název**skupiny prostředků # [požadováno pro Version < 0.0.4] skupiny prostředků trezoru klíčů.
* **SubscriptionId**: ID předplatného vašeho trezoru klíčů # [požadováno pro Version < 0.0.4] ID předplatného trezoru klíčů.
* **tenantID**: ID klienta nebo ID adresáře vašeho trezoru klíčů

Dokumentace všech povinných polí je k dispozici zde: [odkaz](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

Aktualizovaná šablona je uvedena v následujícím kódu. Stáhněte si ho jako soubor YAML a vyplňte požadovaná pole. V tomto příkladu je Trezor klíčů **contosoKeyVault5**. Má dva tajné klíče, **secret1** a **secret2**.

> [!NOTE] 
> Pokud používáte spravované identity, nastavte hodnotu **usePodIdentity** na *true*a jako dvojici uvozovek (**""**) nastavte hodnotu **userAssignedIdentityID** . 

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
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
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

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Přiřazení instančního objektu nebo použití spravovaných identit

### <a name="assign-a-service-principal"></a>Přiřazení instančního objektu

Pokud používáte instanční objekt, udělte mu oprávnění pro přístup k trezoru klíčů a načtení tajných kódů. Přiřaďte roli *Čtenář* a udělte instančnímu objektu oprávnění k *získání* tajných kódů z trezoru klíčů pomocí následujícího příkazu:

1. Přiřaďte instanční objekt k existujícímu trezoru klíčů. Parametr **$AZURE _CLIENT_ID** je identifikátor **appId** , který jste zkopírovali po vytvoření instančního objektu.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Výstup příkazu je zobrazen na následujícím obrázku: 

    ![Snímek obrazovky zobrazující hodnotu principalId](../media/kubernetes-key-vault-5.png)

1. Udělte instančnímu objektu oprávnění k získání tajných kódů:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    az keyvault set-policy -n $KEYVAULT_NAME --key-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Nyní jste nakonfigurovali instanční objekt s oprávněním ke čtení tajných kódů z trezoru klíčů. **_CLIENT_SECRET $Azure** je heslo k instančnímu objektu. Přidejte přihlašovací údaje instančního objektu jako tajný kód Kubernetes, který je přístupný ovladači pro ukládání tajných klíčů.
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Pokud nasazujete Kubernetes pod a obdržíte chybu týkající se neplatného ID tajného klíče klienta, můžete mít starší ID tajného klíče klienta, jehož platnost vypršela nebo resetovat. Pokud chcete tento problém vyřešit, odstraňte tajné klíče *-Store-přihlašovací* údaje a vytvořte nový s aktuálním ID tajného kódu klienta. Pokud chcete odstranit *tajná klíčová úložiště – přihlašovací*údaje, spusťte následující příkaz:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Pokud jste zapomněli ID tajného kódu klienta objektu služby, můžete ho resetovat pomocí následujícího příkazu:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Použití spravovaných identit

Pokud používáte spravované identity, přiřaďte ke clusteru AKS, který jste vytvořili, konkrétní role. 

1. Pokud chcete vytvořit, vypsat nebo načíst spravovanou identitu přiřazenou uživatelem, musí mít váš cluster AKS přiřazenou roli [spravovaného operátoru identity](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Ujistěte se, že **$ClientID** je ClientID clusteru Kubernetes. V případě tohoto oboru se bude jednat o službu předplatného Azure, konkrétně skupinu prostředků uzlu, která byla vytvořena při vytvoření clusteru AKS. Tento rozsah zajistí, že role přiřazené níže mají vliv jenom na prostředky v této skupině. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
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
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Nasazení zařízení s připojenými tajnými kódy z trezoru klíčů

Pokud chcete nakonfigurovat objekt SecretProviderClass, spusťte následující příkaz:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Použití instančního objektu

Pokud používáte instanční objekt, pomocí následujícího příkazu nasaďte Kubernetes lusky s SecretProviderClass a tajnými klíči-Store-přihlašovací údaje, které jste nakonfigurovali dříve. Tady jsou šablony nasazení:
* Pro [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* Pro [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
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
        nodePublishSecretRef:           # Only required when using service principal mode
          name: secrets-store-creds     # Only required when using service principal mode
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
> [Zapnout obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/general/soft-delete-cli)
