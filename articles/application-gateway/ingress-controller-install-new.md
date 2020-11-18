---
title: Vytvoření kontroleru příchozího přenosu dat pomocí nového Application Gateway
description: Tento článek poskytuje informace o tom, jak nasadit Application Gateway řadič příchozího přenosu dat pomocí nového Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5e3473a9afefe73fe7b07d3efda1f53675264fc8
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874623"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Jak nainstalovat Application Gateway AGIC (příchozí adaptér) pomocí nového Application Gateway

Níže uvedené pokyny předpokládají, Application Gateway řadič příchozího přenosu dat (AGIC) se nainstaluje do prostředí bez již existujících součástí.

## <a name="required-command-line-tools"></a>Požadované nástroje příkazového řádku

Pro všechny operace příkazového řádku doporučujeme použít [Azure Cloud Shell](https://shell.azure.com/) . Spusťte prostředí z shell.azure.com nebo kliknutím na odkaz:

[![Vložené spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

Případně můžete Cloud Shell z Azure Portal spustit pomocí následující ikony:

![Spuštění portálu](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Vaše [Azure Cloud Shell](https://shell.azure.com/) už má všechny potřebné nástroje. Pokud se rozhodnete použít jiné prostředí, zkontrolujte, že jsou nainstalované následující nástroje příkazového řádku:

* `az` – Azure CLI: [pokyny k instalaci](/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` -Kubernetes nástroj příkazového řádku: [pokyny k instalaci](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` – Správce balíčků Kubernetes: [pokyny k instalaci](https://github.com/helm/helm/releases/latest)
* `jq` -příkazový řádek JSON – procesor: [pokyny k instalaci](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Vytvoření identity

Pomocí následujících kroků vytvořte [objekt instančního objektu služby](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)Azure Active Directory (AAD). Poznamenejte si `appId` hodnoty, a, které `password` `objectId` budou použity v následujících krocích.

1. Vytvoření instančního objektu služby AD ([Další informace o službě Azure RBAC](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    `appId`Hodnoty a `password` z výstupu JSON se použijí v následujících krocích.


1. Použijte `appId` výstup z předchozího příkazu k získání `objectId` nového instančního objektu:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Výstup tohoto příkazu je `objectId` , který se použije v Azure Resource Manager šabloně níže.

1. Vytvořte soubor parametrů, který bude použit v nasazení šablony Azure Resource Manager později.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Pokud chcete nasadit cluster s povoleným **KUBERNETES RBAC** , nastavte `aksEnableRBAC` pole na `true`

## <a name="deploy-components"></a>Nasadit součásti
Tento krok přidá do svého předplatného tyto komponenty:

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- [Virtual Network](../virtual-network/virtual-networks-overview.md) se dvěma [podsítěmi](../virtual-network/virtual-networks-overview.md)
- [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
- [Spravovaná identita](../active-directory/managed-identities-azure-resources/overview.md), kterou bude používat [Identita AAD pod](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Stáhněte šablonu Azure Resource Manager a podle potřeby upravte šablonu.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Nasaďte šablonu Azure Resource Manager pomocí `az cli` . To může trvat až 5 minut.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Po dokončení nasazení stáhněte výstup nasazení do souboru s názvem `deployment-outputs.json` .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Nastavení ovladače Application Gateway příchozího přenosu dat

Podle pokynů v předchozí části jsme vytvořili a nakonfigurovali nový cluster AKS a Application Gateway. Nyní jsme připraveni nasadit ukázkovou aplikaci a vstupní kontroler do naší nové infrastruktury Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Nastavení přihlašovacích údajů Kubernetes
Pro následující kroky potřebujeme příkaz Setup [kubectl](https://kubectl.docs.kubernetes.io/) , který použijeme pro připojení k naší novému clusteru Kubernetes. [Cloud Shell](https://shell.azure.com/) `kubectl` už je nainstalovaný. `az`K získání přihlašovacích údajů pro Kubernetes budeme používat rozhraní příkazového řádku.

Získání přihlašovacích údajů pro nově nasazené AKS ([Další informace](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Nainstalovat identitu AAD pod
  Azure Active Directory pod identitou poskytuje přístup založený na tokenech k [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

  [Identita AAD pod](https://github.com/Azure/aad-pod-identity) přidá do clusteru Kubernetes následující součásti:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity` , `AzureAssignedIdentity` , `AzureIdentityBinding`
   * Komponenta [spravovaného řadiče identity (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Komponenta [spravované identity (NMI) uzlů](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Instalace identity AAD pod do clusteru:

   - *KUBERNETES RBAC povolena* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *KUBERNETES RBAC je zakázaná* . Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Instalace nástroje Helm
[Helm](../aks/kubernetes-helm.md) je správce balíčků pro Kubernetes. Použijeme ji k instalaci `application-gateway-kubernetes-ingress` balíčku:

1. Nainstalujte [Helm](../aks/kubernetes-helm.md) a spusťte následující příkaz pro přidání `application-gateway-kubernetes-ingress` balíčku Helm:

    - *KUBERNETES RBAC povolena* Cluster AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *KUBERNETES RBAC je zakázaná* . Cluster AKS

        ```bash
        helm init
        ```

1. Přidejte úložiště AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Helm graf instalace adaptéru příchozího přenosu dat

1. Použijte `deployment-outputs.json` soubor vytvořený výše a vytvořte následující proměnné.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Stáhněte si Helm-config. yaml, který bude konfigurovat AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Nebo zkopírujte následující soubor YAML: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Upravte nově stažený soubor Helm-config. yaml a vyplňte oddíly `appgw` a `armAuth` .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Hodnoty:
     - `verbosityLevel`: Nastaví úroveň podrobností infrastruktury protokolování AGIC. Možné hodnoty najdete v tématu [úrovně protokolování](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) .
     - `appgw.subscriptionId`: ID předplatného Azure, ve kterém se Application Gateway nachází. Příklad: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Název skupiny prostředků Azure, ve které se vytvořil Application Gateway. Příklad: `app-gw-resource-group`
     - `appgw.name`: Název Application Gateway. Příklad: `applicationgatewayd0f0`
     - `appgw.shared`: Tento logický příznak by měl být nastaven na výchozí hodnotu `false` . Nastavte na, `true` Pokud potřebujete [sdílený Application Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Zadejte obor názvů, který má AGIC sledovat. Může se jednat o jednu řetězcovou hodnotu nebo seznam oborů názvů oddělených čárkami.
    - `armAuth.type`: může být `aadPodIdentity` nebo `servicePrincipal`
    - `armAuth.identityResourceID`: ID prostředku spravované identity Azure
    - `armAuth.identityClientId`: ID klienta identity. Další informace o identitě najdete níže.
    - `armAuth.secretJSON`: Je potřeba jenom v případě, že je zvolený tajný typ objektu služby (Pokud `armAuth.type` byl nastavený na `servicePrincipal` ). 


   > [!NOTE]
   > `identityResourceID`A `identityClientID` jsou hodnoty, které byly vytvořeny během kroků [nasazení součástí](ingress-controller-install-new.md#deploy-components) , a lze je znovu získat pomocí následujícího příkazu:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` v příkazu výše je skupina prostředků vašeho Application Gateway. `<identity-name>` je název vytvořené identity. Všechny identity pro dané předplatné můžou být uvedené pomocí: `az identity list`


1. Nainstalujte balíček Application Gateway příchozího řadiče pro příchozí přenosy:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instalace ukázkové aplikace
Teď, když máme nainstalovanou Application Gateway, AKS a AGIC, můžeme pomocí [Azure Cloud Shell](https://shell.azure.com/)nainstalovat ukázkovou aplikaci:

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Případně můžete:

* Stáhněte si soubor YAML výše:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Použít soubor YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Další příklady
Tato [Příručka](ingress-controller-expose-service-over-http-https.md) obsahuje další příklady, jak vystavit službu AKS přes HTTP nebo https, na Internet s Application Gateway.