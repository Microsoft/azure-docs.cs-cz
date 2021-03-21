---
title: Vytvoření kontroleru příchozího přenosu s existující Application Gateway
description: Tento článek poskytuje informace o tom, jak nasadit Application Gateway řadič příchozího přenosu s existujícím Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2d64766c754c0ea104ae83fde799a514e9da6d68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693736"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instalace AGIC (příchozího adaptéru Application Gateway) pomocí existující Application Gateway

Kontroler příchozího přenosu Application Gateway (AGIC) je pod clusterem Kubernetes.
AGIC [sleduje prostředky](https://kubernetes.io/docs/concepts/services-networking/ingress/) příchozího přenosu dat Kubernetes a vytváří a používá Application Gateway konfiguraci na základě stavu clusteru Kubernetes.

## <a name="outline"></a>Obrysu
- [Požadavky](#prerequisites)
- [Ověřování Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Možnost 1: [Nastavení AAD-pod-identity](#set-up-aad-pod-identity) a vytvoření identity Azure na zbraních
    - Možnost 2: [použití instančního objektu](#using-a-service-principal)
- [Instalace kontroleru příchozího přenosu dat pomocí Helm](#install-ingress-controller-as-a-helm-chart)
- [Více clusterů a sdílených Application Gateway](#multi-cluster--shared-application-gateway): Nainstalujte AGIC do prostředí, kde Application Gateway se sdílí mezi jedním nebo více clustery AKS a/nebo jinými součástmi Azure.

## <a name="prerequisites"></a>Předpoklady
V tomto dokumentu se předpokládá, že už máte nainstalované tyto nástroje a infrastrukturu:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) s povolenými [pokročilými sítěmi](../aks/configure-azure-cni.md)
- [Application Gateway v2](./tutorial-autoscale-ps.md) ve stejné virtuální síti jako AKS
- [Identita AAD pod](https://github.com/Azure/aad-pod-identity) nainstalovanou v clusteru AKS
- [Cloud Shell](https://shell.azure.com/) je prostředí Azure shell s `az` nainstalovaným rozhraním CLI, `kubectl` a `helm` . Tyto nástroje jsou vyžadovány pro následující příkazy.

Před instalací AGIC prosím __zálohujte konfiguraci Application Gateway__ :
  1. použití [Azure Portal](https://portal.azure.com/) přechodem k `Application Gateway` instanci
  2. `Export template`kliknutím`Download`

Stažený soubor zip bude mít šablony JSON, bash a PowerShellové skripty, které byste mohli použít k obnovení brány App Gateway, aby bylo nezbytné.

## <a name="install-helm"></a>Instalace nástroje Helm
[Helm](../aks/kubernetes-helm.md) je správce balíčků pro Kubernetes. Použijeme ji k instalaci `application-gateway-kubernetes-ingress` balíčku.
K instalaci Helm použijte [Cloud Shell](https://shell.azure.com/) :

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

## <a name="azure-resource-manager-authentication"></a>Ověřování Azure Resource Manager

AGIC komunikuje se serverem rozhraní Kubernetes API a Azure Resource Manager. Pro přístup k těmto rozhraním API vyžaduje identitu.

## <a name="set-up-aad-pod-identity"></a>Nastavení identity AAD pod

[Identita AAD pod](https://github.com/Azure/aad-pod-identity) je kontroler, podobně jako AGIC, který se taky spouští na AKS. Naváže Azure Active Directory identity k Kubernetes luskům. Aby aplikace v Kubernetes pod mohla komunikovat s dalšími komponentami Azure, vyžaduje se identita. V takovém případě potřebujeme autorizaci pro AGIC pod, aby bylo možné požadavky HTTP na [ARM](../azure-resource-manager/management/overview.md).

Postupujte podle [pokynů pro instalaci služby AAD pod](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) , abyste přidali tuto komponentu do AKS.

Dál musíme vytvořit identitu Azure a dát jí oprávnění ARM.
Pomocí [Cloud Shell](https://shell.azure.com/) můžete spustit všechny následující příkazy a vytvořit identitu:

1. Vytvořte identitu Azure **ve stejné skupině prostředků jako uzly AKS**. Je důležité vybrat správnou skupinu prostředků. Skupina prostředků požadovaná v příkazu *níže není ta, na* kterou se odkazuje v podokně AKS Portal. Toto je skupina prostředků `aks-agentpool` virtuálních počítačů. Obvykle se skupina prostředků spouští s `MC_` a obsahuje název vaší AKS. Např.: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Pro níže uvedené příkazy přiřazení role je potřeba získat `principalId` pro nově vytvořenou identitu:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Udělte identitě `Contributor` přístup k vašemu Application Gateway. Za tímto účelem budete potřebovat ID Application Gateway, který bude vypadat přibližně takto: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Seznam ID Application Gateway v předplatném získáte pomocí: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Udělte identitě `Reader` přístup ke skupině prostředků Application Gateway. ID skupiny prostředků by vypadalo takto: `/subscriptions/A/resourceGroups/B` . Všechny skupiny prostředků můžete získat takto: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Použití instančního objektu
Také je možné poskytnout AGIC přístup k ARM prostřednictvím tajného klíče Kubernetes.

1. Vytvoření instančního objektu služby Active Directory a kódování Base64. Pro uložení objektu BLOB JSON do Kubernetes je vyžadováno kódování Base64.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Přidejte do souboru objekt BLOB JSON kódovaný jako base64 `helm-config.yaml` . Další informace najdete `helm-config.yaml` v následující části.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instalace kontroleru příchozího přenosu dat jako Helmový graf
V prvních několika krocích nainstalujeme do vašeho clusteru Kubernetes do Helm. K instalaci balíčku AGIC Helm použijte [Cloud Shell](https://shell.azure.com/) :

1. Přidání `application-gateway-kubernetes-ingress` úložiště Helm a provedení aktualizace Helm

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Upravte Helm-config. yaml a vyplňte hodnoty pro `appgw` a `armAuth` .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>`A `<identity-client-id>` jsou vlastnosti identity Azure AD, které jste nastavili v předchozí části. Tyto informace můžete načíst spuštěním následujícího příkazu: `az identity show -g <resourcegroup> -n <identity-name>` , kde `<resourcegroup>` je skupina prostředků, ve které je nasazený objekt AKS clusteru nejvyšší úrovně, Application Gateway a spravované identifikace.

1. Nainstalovat graf Helm `application-gateway-kubernetes-ingress` s `helm-config.yaml` konfigurací z předchozího kroku

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternativně můžete kombinovat `helm-config.yaml` a příkaz Helm v jednom kroku:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Zkontrolujte protokol nově vytvořeného ovládacího panelu a ověřte, zda bylo spuštěno správně.

V [této příručce](ingress-controller-expose-service-over-http-https.md) najdete informace o tom, jak můžete vystavit službu AKS přes HTTP nebo https na internet pomocí Application Gateway Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Více clusterů a sdílených Application Gateway
Ve výchozím nastavení předpokládá AGIC plné vlastnictví Application Gateway je propojena s. AGIC verze 0.8.0 a novější může sdílet jeden Application Gateway s dalšími komponentami Azure. Například můžeme použít stejný Application Gateway pro aplikaci hostovanou v sadě škálování virtuálního počítače i v clusteru AKS.

Než povolíte toto nastavení, __zálohujte prosím konfiguraci Application Gateway__ :
  1. použití [Azure Portal](https://portal.azure.com/) přechodem k `Application Gateway` instanci
  2. `Export template`kliknutím`Download`

Stažený soubor zip bude mít šablony JSON, bash a PowerShellové skripty, které můžete použít k obnovení Application Gateway

### <a name="example-scenario"></a>Ukázkový scénář
Pojďme se podívat na imaginární Application Gateway, který spravuje provoz dvou webů:
  - `dev.contoso.com` – hostovaná na novém AKS pomocí Application Gateway a AGIC
  - `prod.contoso.com` – hostovaná v [sadě škálování virtuálních počítačů Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Ve výchozím nastavení předpokládá AGIC 100% vlastnictví Application Gateway, na které se odkazuje. AGIC Přepisuje veškerou konfiguraci služby App Gateway. Pokud jsme museli ručně vytvořit naslouchací proces pro `prod.contoso.com` (na Application Gateway), aniž byste ho definovali v rámci Kubernetes příchozího přenosu dat, AGIC `prod.contoso.com` během několika sekund tuto konfiguraci odstraní.

Aby bylo možné nainstalovat AGIC a taky `prod.contoso.com` z našich počítačů s měřítkem sady virtuálních počítačů, je potřeba omezit AGIC jenom na konfiguraci `dev.contoso.com` . To se usnadňuje vytvořením instance následujícího [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Výše uvedený příkaz vytvoří `AzureIngressProhibitedTarget` objekt. Díky tomu AGIC (verze 0.8.0 a novější) si vědomi existence Application Gateway konfigurace pro `prod.contoso.com` a explicitně ji instruuje, aby nedošlo ke změně konfigurace související s tímto názvem hostitele.


### <a name="enable-with-new-agic-installation"></a>Povolit s novou instalací AGIC
Chcete-li omezit AGIC (verze 0.8.0 a novější) na podmnožinu konfigurace Application Gateway upravte `helm-config.yaml` šablonu.
V `appgw:` části přidejte `shared` klíč a nastavte ho na `true` .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Použít změny Helm:
  1. Ujistěte se, že `AzureIngressProhibitedTarget` je CRD nainstalovaný s:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Helm aktualizace:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

V důsledku toho bude mít vaše AKS novou instanci s `AzureIngressProhibitedTarget` názvem `prohibit-all-targets` :
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Objekt `prohibit-all-targets` , jak název implikuje, zakazuje AGIC změnu konfigurace pro *libovolného* hostitele a cestu.
Helm nainstalujete s `appgw.shared=true` tím, že nasadíte AGIC, ale neprovede žádné změny Application Gateway.


### <a name="broaden-permissions"></a>Rozšířit oprávnění
Vzhledem k tomu, že Helm s `appgw.shared=true` a výchozími `prohibit-all-targets` bloky AGIC použití jakékoli konfigurace.

Rozšířit AGIC oprávnění pomocí:
1. Vytvořte novou `AzureIngressProhibitedTarget` pomocí konkrétního nastavení:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Pouze po vytvoření vlastního zákazu můžete odstranit výchozí, což je příliš široké:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Povolit pro existující instalaci AGIC
Předpokládejme, že už máme v našem clusteru funkční AKS, Application Gateway a nakonfigurovaný AGIC. Máme příchozí `prod.contoso.com` data pro a úspěšně obsluhují provoz z AKS. Chceme přidat `staging.contoso.com` k naší existující Application Gateway, ale je potřeba ho hostovat na [virtuálním počítači](https://azure.microsoft.com/services/virtual-machines/). Znovu použijeme existující Application Gateway a ručně nakonfigurujete naslouchací proces a fond back-end pro `staging.contoso.com` . Ale ruční úprava Application Gateway konfigurace (prostřednictvím [portálu](https://portal.azure.com), [rozhraní API ARM](/rest/api/resources/) nebo [terraformu](https://www.terraform.io/)) je v konfliktu se předpoklady úplného vlastnictví AGIC. Krátce po použití změn se AGIC přepíše nebo odstraní.

AGIC můžeme zabránit v provádění změn v podmnožině konfigurace.

1. Vytvořit `AzureIngressProhibitedTarget` objekt:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Zobrazení nově vytvořeného objektu:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Úprava Application Gateway config prostřednictvím portálu – přidejte naslouchací procesy, pravidla směrování, back-endy atd. Nový objekt, který jsme vytvořili ( `manually-configured-staging-environment` ), zakazuje AGIC přepisu Application Gateway konfigurace související s `staging.contoso.com` .
