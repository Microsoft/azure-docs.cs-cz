---
title: Vytvoření řadiče příchozího přenosu dat s novou aplikační bránou
description: Tento článek obsahuje informace o tom, jak nasadit řadič příchozího přenosu dat aplikační brány s novou aplikační bránou.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b46c9f8b0cad74f3a4e9be8903270a60993c01f4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585887"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Jak nainstalovat řadič příchozího přenosu dat aplikační brány (AGIC) pomocí nové aplikační brány

Níže uvedené pokyny předpokládají, že řadič příchozího přenosu dat (Application Gateway Ingress Controller) bude nainstalován v prostředí bez již existujících součástí.

## <a name="required-command-line-tools"></a>Požadované nástroje příkazového řádku

Doporučujeme použití [Azure Cloud Shell](https://shell.azure.com/) pro všechny operace příkazového řádku níže. Spusťte svůj shell z shell.azure.com nebo kliknutím na odkaz:

[![Spuštění vložení](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

Případně můžete spustit Cloud Shell z webu Azure portal pomocí následující ikony:

![Spuštění portálu](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Azure [Cloud Shell](https://shell.azure.com/) už má všechny potřebné nástroje. Pokud se rozhodnete použít jiné prostředí, zkontrolujte, zda jsou nainstalovány následující nástroje příkazového řádku:

* `az`- Azure CLI: [pokyny k instalaci](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Kubernetes příkaz-line nástroj: [návod k instalaci](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Správce balíčků Kubernetes: [pokyny k instalaci](https://github.com/helm/helm/releases/latest)
* `jq`- procesor JSON příkazového řádku: [pokyny k instalaci](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Vytvoření identity

Podle následujících kroků vytvořte [objekt zaregistrovaný objekt služby](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)Azure Active Directory (AAD). Zaznamenejte `appId`, `password`, a `objectId` hodnoty - tyto budou použity v následujících krocích.

1. Vytvořit instanční objekt služby AD[(Přečtěte si více o RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Hodnoty `appId` `password` a z výstupu JSON budou použity v následujících krocích


1. Pomocí `appId` výstupu z předchozího příkazu `objectId` získat nový instanční objekt:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Výstup tohoto příkazu `objectId`je , který se použije v šabloně Azure Resource Manager níže

1. Vytvořte soubor parametrů, který se bude později používat v nasazení šablony Azure Resource Manageru.
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
    Chcete-li nasadit cluster s `aksEnabledRBAC` podporou **RBAC,** nastavte pole na`true`

## <a name="deploy-components"></a>Nasazení součástí
Tento krok přidá do předplatného následující součásti:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Aplikační brána](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Virtuální síť](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) se 2 [podsítěmi](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Veřejná IP adresa](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Spravovaná identita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), kterou bude používat [identita pod aad](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Stáhněte si šablonu Azure Resource Manager a podle potřeby ji upravte.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Nasazení šablony Azure Resource `az cli`Manager pomocí . To může trvat až 5 minut.
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

1. Po dokončení nasazení stáhněte výstup nasazení do `deployment-outputs.json`souboru s názvem .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Nastavení řadiče příchozího přenosu dat aplikační brány

S pokyny v předchozí části jsme vytvořili a nakonfigurovali nový cluster AKS a aplikační bránu. Teď jsme připraveni nasadit ukázkovou aplikaci a řadič příchozího přenosu dat do naší nové infrastruktury Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Nastavení pověření Kubernetes
Pro následující kroky potřebujeme příkaz [nastavení kubectl,](https://kubectl.docs.kubernetes.io/) který použijeme pro připojení k našemu novému clusteru Kubernetes. [Cloud](https://shell.azure.com/) Shell `kubectl` již nainstalován. K získání `az` přihlašovacích údajů pro Kubernetes použijeme cli.

Získejte přihlašovací údaje pro nově nasazený AKS[(přečtěte si více):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Instalace identity podu AAD
  Azure Active Directory Pod Identity poskytuje přístup založený na tokenech ke [Správci prostředků Azure (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) přidá do clusteru Kubernetes následující součásti:
   * [Krsované kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity` `AzureAssignedIdentity`: , ,`AzureIdentityBinding`
   * Součást [Spravovaného řadiče identity (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Součást [Identity spravované uzlu (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Instalace identity podu AAD do clusteru:

   - *RBAC povolena* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC zakázáno* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Instalace helmu
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) je správce balíčků pro Kubernetes. Využijeme jej k `application-gateway-kubernetes-ingress` instalaci balíčku:

1. Nainstalujte [helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) a spusťte následující přidat balíček `application-gateway-kubernetes-ingress` helmu:

    - *RBAC povolena* Cluster AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC zakázáno* Cluster AKS

        ```bash
        helm init
        ```

1. Přidejte úložiště AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Instalace helmového grafu řadiče příchozího přenosu dat

1. Použijte `deployment-outputs.json` výše vytvořený soubor a vytvořte následující proměnné.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Stáhněte si helm-config.yaml, který bude konfigurovat AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Nebo zkopírujte soubor YAML níže: 
    
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
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Upravte nově staženou helm-config.yaml a `appgw` `armAuth`vyplňte oddíly a .
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
     - `verbosityLevel`: Nastaví úroveň podrobností infrastruktury protokolování AGIC. Možné hodnoty naleznete [v tématu Úrovně protokolování.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)
     - `appgw.subscriptionId`: ID předplatného Azure, ve kterém se nachází aplikační brána. Příklad: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Název skupiny prostředků Azure, ve kterém byla vytvořena aplikační brána. Příklad: `app-gw-resource-group`
     - `appgw.name`: Název aplikační brány. Příklad: `applicationgatewayd0f0`
     - `appgw.shared`: Tento logický příznak by `false`měl být výchozí pro . Nastavte `true` na pokud potřebujete [sdílenou aplikační bránu](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Zadejte obor názvů, který by měl AGIC sledovat. Může se jednalo o hodnotu jednoho řetězce nebo o seznam oborů názvů oddělených čárkami.
    - `armAuth.type`: může `aadPodIdentity` být nebo`servicePrincipal`
    - `armAuth.identityResourceID`: ID prostředku spravované identity Azure
    - `armAuth.identityClientId`: ID klienta identity. Další informace o identitě naleznete níže
    - `armAuth.secretJSON`: Je potřeba pouze v případě, `armAuth.type` že je `servicePrincipal`vybrán typ tajného objektu hlavního servisu (pokud byla nastavena na ) 


   > [!NOTE]
   > A `identityResourceID` `identityClientID` jsou hodnoty, které byly vytvořeny během kroků Nasazení součástí a mohly být znovu [získány](ingress-controller-install-new.md#deploy-components) pomocí následujícího příkazu:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`ve výše uvedeném příkazu je skupina prostředků vaší aplikační brány. `<identity-name>`je název vytvořené identity. Všechny identity pro dané předplatné mohou být uvedeny pomocí:`az identity list`


1. Nainstalujte balíček řadiče příchozího přenosu dat aplikační brány:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instalace ukázkové aplikace
Teď, když máme nainstalovanou aplikační bránu, AKS a AGIC, můžeme nainstalovat ukázkovou aplikaci přes [Azure Cloud Shell](https://shell.azure.com/):

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

* Stáhněte si výše uvedený soubor YAML:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Použijte soubor YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Další příklady
Tento [návod](ingress-controller-expose-service-over-http-https.md) obsahuje další příklady, jak vystavit službu AKS prostřednictvím protokolu HTTP nebo HTTPS, k Internetu s aplikační bránou.
