---
title: Vytvoření řadiče příchozího přenosu dat s existující aplikační bránou
description: Tento článek obsahuje informace o tom, jak nasadit řadič příchozího přenosu dat aplikační brány s existující aplikační bránou.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 949f1b3ee3db72e1c541c3dd4c5f74f364f1b514
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869896"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instalace řadiče příchozího přenosu dat aplikační brány (AGIC) pomocí existující aplikační brány

Řadič příchozího přenosu dat aplikační brány (AGIC) je pod v rámci clusteru Kubernetes.
AGIC monitoruje prostředky [Příchozí přenos](https://kubernetes.io/docs/concepts/services-networking/ingress/) dat Kubernetes a vytváří a aplikuje konfigurace aplikační brány na základě stavu clusteru Kubernetes.

## <a name="outline"></a>Osnovy:
- [Požadované součásti](#prerequisites)
- [Ověřování azure správce prostředků (ARM)](#azure-resource-manager-authentication)
    - Možnost 1: [Nastavení identity aad-pod](#set-up-aad-pod-identity) a vytvoření identity Azure na modulech ARM
    - Možnost 2: [Použití instančního objektu](#using-a-service-principal)
- [Instalace řadiče příchozího přenosu dat pomocí helmy](#install-ingress-controller-as-a-helm-chart)
- [Multicluster / Shared Application Gateway](#multi-cluster--shared-application-gateway): Instalace AGIC v prostředí, kde je aplikační brána sdílena mezi jedním nebo více clustery AKS nebo jinými součástmi Azure.

## <a name="prerequisites"></a>Požadované součásti
Tento dokument předpokládá, že již máte nainstalovány následující nástroje a infrastrukturu:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) s [povolenou pokročilou sítí](https://docs.microsoft.com/azure/aks/configure-azure-cni)
- [Aplikační brána v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) ve stejné virtuální síti jako AKS
- [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) nainstalované v clusteru AKS
- [Cloud Shell](https://shell.azure.com/) je prostředí prostředí `az` Azure, `kubectl`které `helm` má příkazové příkazové příkazy a nainstalované. Tyto nástroje jsou vyžadovány pro níže uvedené příkazy.

Před instalací rozhraní AGIC __zálohujte konfiguraci aplikační brány:__
  1. pomocí [portálu](https://portal.azure.com/) Azure `Application Gateway` přejděte na vaši instanci
  2. z `Export template` kliknutí`Download`

Soubor zip, který jste stáhli, bude mít šablony JSON, bash a skripty prostředí PowerShell, které byste mohli použít k obnovení brány aplikace, pokud by to bylo nutné

## <a name="install-helm"></a>Instalace helmu
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) je správce balíčků pro Kubernetes. Využijeme ho k `application-gateway-kubernetes-ingress` instalaci balíčku.
K instalaci helmu použijte [cloudové prostředí:](https://shell.azure.com/)

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

## <a name="azure-resource-manager-authentication"></a>Ověřování azure správce prostředků

AGIC komunikuje se serverem rozhraní API Kubernetes a Správcem prostředků Azure. Vyžaduje identitu pro přístup k těmto api.

## <a name="set-up-aad-pod-identity"></a>Nastavení identity podu AAD

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) je řadič, podobně jako AGIC, který také běží na vašem AKS. Váže identity Služby Azure Active Directory s pody Kubernetes. Identita je vyžadována pro aplikaci v podu Kubernetes, aby bylo možné komunikovat s jinými součástmi Azure. V konkrétním případě zde potřebujeme povolení pro AGIC pod, aby se HTTP požadavky [arm](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Podle [pokynů k instalaci aad pod identity](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) přidat tuto komponentu do AKS.

Dále musíme vytvořit identitu Azure a dát jí oprávnění ARM.
Pomocí [prostředí Cloud Shell](https://shell.azure.com/) spusťte všechny následující příkazy a vytvořte identitu:

1. Vytvořte identitu Azure **ve stejné skupině prostředků jako uzly AKS**. Výběr správné skupiny prostředků je důležitý. Skupina prostředků požadovaná v níže uvedeném příkazu *není* skupina, na kterou se odkazuje v podokně portálu AKS. Toto je skupina `aks-agentpool` prostředků virtuálních počítačů. Tato skupina prostředků obvykle `MC_` začíná a obsahuje název aks. Například:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Pro níže uvedené příkazy přiřazení `principalId` rolí musíme získat pro nově vytvořenou identitu:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Pouzdnejpřístup `Contributor` k vaší aplikační bráně. K tomu potřebujete ID aplikační brány, která bude vypadat nějak takto:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Získejte seznam ID aplikační brány ve vašem předplatném s:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Udělit identitu `Reader` přístup ke skupině prostředků aplikační brány. ID skupiny prostředků bude `/subscriptions/A/resourceGroups/B`vypadat takto: . Všechny skupiny prostředků můžete získat pomocí:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Použití instančního objektu
Je také možné poskytnout AGIC přístup k ARM přes tajemství Kubernetes.

1. Vytvořte zaregistrovaný objekt služby Active Directory a kódujte pomocí base64. Kódování base64 je vyžadováno pro objekt blob JSON, který má být uložen do Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Přidejte do `helm-config.yaml` souboru objekt blob SSON kódovaný base64. Více informací `helm-config.yaml` je v další části.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instalace řadiče příchozího přenosu dat jako grafu helmu
V prvních několika krocích nainstalujeme Helm's Tiller do vašeho clusteru Kubernetes. K instalaci balíčku AGIC Helm použijte [cloudové prostředí:](https://shell.azure.com/)

1. Přidejte `application-gateway-kubernetes-ingress` repo kormidla a proveďte aktualizaci kormidla

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Upravte helm-config.yaml a vyplňte hodnoty pro `appgw` a `armAuth`.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > A `<identity-resource-id>` `<identity-client-id>` jsou vlastnosti identity Azure AD, kterou nastavíte v předchozí části. Tyto informace můžete načíst spuštěním `az identity show -g <resourcegroup> -n <identity-name>`následujícího příkazu: , kde `<resourcegroup>` je skupina prostředků, ve které se nasadí objekt clusteru AKS nejvyšší úrovně, aplikační brána a spravovaná identifikace.

1. Instalace grafu `application-gateway-kubernetes-ingress` Helm `helm-config.yaml` s konfigurací z předchozího kroku

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Případně můžete zkombinovat `helm-config.yaml` příkaz A Helm v jednom kroku:
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

1. Zkontrolujte protokol nově vytvořeného podu a ověřte, zda byl správně spuštěn.

Naleznete v [tomto návodu](ingress-controller-expose-service-over-http-https.md) k pochopení, jak můžete vystavit službu AKS přes HTTP nebo HTTPS, na internet pomocí brány aplikace Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Víceclusterová / sdílená aplikační brána
Ve výchozím nastavení AGIC přebírá plné vlastnictví aplikační brány, se kterou je propojena. AGIC verze 0.8.0 a novější můžete sdílet jednu aplikační bránu s ostatními součástmi Azure. Například bychom mohli použít stejnou aplikační bránu pro aplikaci hostovohoujicí na škálovací sadě virtuálních strojů a také pro cluster AKS.

Před povolením tohoto nastavení __zálohujte konfiguraci aplikační brány:__
  1. pomocí [portálu](https://portal.azure.com/) Azure `Application Gateway` přejděte na vaši instanci
  2. z `Export template` kliknutí`Download`

Soubor zip, který jste stáhli, bude mít šablony JSON, bash a skripty prostředí PowerShell, které můžete použít k obnovení aplikační brány

### <a name="example-scenario"></a>Ukázkový scénář
Podívejme se na imaginární aplikační bránu, která spravuje provoz pro dva weby:
  - `dev.contoso.com`- hostované na novém AKS, pomocí Aplikační brána a AGIC
  - `prod.contoso.com`- hostované na [škálovací sadě virtuálních strojů Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

S výchozím nastavením AGIC přebírá 100% vlastnictví aplikační brány, na kterou je odkazováno. AGIC přepíše všechny konfigurace Služby App Gateway. Pokud bychom měli ručně vytvořit `prod.contoso.com` naslouchací proces pro (na aplikační brány), bez definování v `prod.contoso.com` Kubernetes Ingress, AGIC odstraní config během několika sekund.

Chcete-li nainstalovat AGIC a také sloužit `prod.contoso.com` z našich počítačů Virtual Machine `dev.contoso.com` Scale Set, musíme omezit AGIC pouze na konfiguraci. To je usnadněno vytvořením instance následující směrnice [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

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

Výše uvedený příkaz `AzureIngressProhibitedTarget` vytvoří objekt. Díky AGIC (verze 0.8.0 a novější) vědomi existence application `prod.contoso.com` gateway config pro a explicitně pokyn, aby se zabránilo změně jakékoli konfigurace související s tímto názvem hostitele.


### <a name="enable-with-new-agic-installation"></a>Povolit s novou instalací AGIC
Chcete-li omezit AGIC (verze 0.8.0 a novější) na `helm-config.yaml` podmnožinu konfigurace aplikační brány, upravte šablonu.
Pod `appgw:` oddílem `shared` přidejte klíč a `true`nastavte ho na .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Použít změny helmu:
  1. Ujistěte `AzureIngressProhibitedTarget` se, že je crd nainstalován s:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Aktualizovat helmu:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

V důsledku toho bude mít Vaše AKS novou instanci `AzureIngressProhibitedTarget` s názvem `prohibit-all-targets`:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Objekt `prohibit-all-targets`, jak název napovídá, zakazuje AGIC změnit konfiguraci pro *všechny* hostitele a cestu.
Instalace helmu s `appgw.shared=true` nasadí AGIC, ale nebude provádět žádné změny v bráně aplikace.


### <a name="broaden-permissions"></a>Rozšířit oprávnění
Vzhledem `appgw.shared=true` k tomu, Helm s a výchozí `prohibit-all-targets` blokuje AGIC z použití libovolnékonfigurace.

Rozšiřte oprávnění AGIC pomocí:
1. Vytvořte `AzureIngressProhibitedTarget` si nový s konkrétním nastavením:
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

2. Teprve po vytvoření vlastního zákazu můžete odstranit výchozí, který je příliš široký:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Povolit pro stávající instalaci AGIC
Předpokládejme, že už máme funkční AKS, aplikační bránu a nakonfigurované AGIC v našem clusteru. Máme příchozí přenos `prod.contosor.com` pro a úspěšně slouží provoz pro něj z AKS. Chceme přidat `staging.contoso.com` do naší stávající aplikační brány, ale je potřeba ji hostovat na [virtuálním počítači](https://azure.microsoft.com/services/virtual-machines/). Budeme znovu použít existující aplikační bránu a ručně nakonfigurovat naslouchací proces a back-endové fondy pro `staging.contoso.com`. Ale ruční ladění application gateway config (přes [portál](https://portal.azure.com), [ARM API](https://docs.microsoft.com/rest/api/resources/) nebo [Terraform](https://www.terraform.io/)) by v rozporu s předpoklady AGIC o plné vlastnictví. Krátce poté, co použijeme změny, AGIC je přepíše nebo odstraní.

AGIC můžeme zakázat provádět změny v podmnožině konfigurace.

1. Vytvoření `AzureIngressProhibitedTarget` objektu:
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

3. Upravit konfiguraon application gateway přes portál - přidat naslouchací procesy, směrovací pravidla, back-endy atd. Nový objekt, který`manually-configured-staging-environment`jsme vytvořili ( ) zakáže `staging.contoso.com`AGIC přepsání konfigurace aplikační brány související s .
