---
title: Povolení více podporuje obor názvů pro řadič příchozího přenosu dat aplikační brány
description: Tento článek obsahuje informace o tom, jak povolit podporu více oborů názvů v clusteru Kubernetes s řadičem příchozího přenosu dat aplikační brány.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279920"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Povolení podpory více oborů názvů v clusteru AKS pomocí řadiče příchozího přenosu dat aplikační brány

## <a name="motivation"></a>Motivace
Kubernetes [Obory názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) umožňují clusterU Kubernetes rozdělit a přidělit podskupinám většího týmu. Tyto podtýmy pak mohou nasadit a spravovat infrastrukturu s jemnějšími kontrolami prostředků, zabezpečení, konfigurace atd. Kubernetes umožňuje jeden nebo více příchozích prostředků, které mají být definovány nezávisle v rámci každého oboru názvů.

Od verze 0.7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) můžete ingestovat události z a sledovat více oborů názvů. Pokud se správce AKS rozhodne použít [bránu aplikace](https://azure.microsoft.com/services/application-gateway/) jako příchozí přenos dat, budou všechny obory názvů používat stejnou instanci aplikační brány. Jedna instalace řadiče ingress bude monitorovat přístupné obory názvů a nakonfiguruje aplikační bránu, ke které je přidružena.

Verze 0.7 AGIC bude nadále `default` výhradně sledovat obor názvů, pokud to není explicitně změněnna jeden nebo více různých oborů názvů v konfiguraci Helm (viz část níže).

## <a name="enable-multiple-namespace-support"></a>Povolení podpory více oborů názvů
Povolení podpory více oborů názvů:
1. upravte soubor [helm-config.yaml](#sample-helm-config-file) jedním z následujících způsobů:
   - zcela `watchNamespace` odstranit klíč z [helm-config.yaml](#sample-helm-config-file) - AGIC bude dodržovat všechny obory názvů
   - nastavit `watchNamespace` na prázdný řetězec - AGIC bude sledovat všechny obory názvů
   - přidat více oborů názvů oddělených`watchNamespace: default,secondNamespace`čárkou ( ) - AGIC bude sledovat výhradně tyto obory názvů
2. použít změny šablony helmu pomocí:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Po nasazení se schopností sledovat více oborů názvů, AGIC bude:
  - seznam ingress prostředků ze všech přístupných oborů názvů
  - filtr pro příchozí zdroje přenosu anotace`kubernetes.io/ingress.class: azure/application-gateway`
  - compose kombinované [aplikační brány config](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - použít konfiguraci na přidruženou aplikační bránu přes [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Konfliktní konfigurace
Více prostředků [příchozího přenosu dat](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) v rámci oboru názvů může dát pokyn AGIC k vytvoření konfliktních konfigurací pro jednu aplikační bránu. (Dva příchozí nároky na stejnou doménu například.)

V horní části hierarchie - **naslouchací procesy** (IP adresa, port a hostitel) a **pravidla směrování** (naslouchací proces vazby, back-endový fond a nastavení HTTP) mohou být vytvořeny a sdíleny více obory názvů/příchozích přenosů dat.

Na druhou stranu - cesty, back-endové fondy, nastavení HTTP a certifikáty TLS mohou být vytvořeny pouze jedním oborem názvů a duplikáty budou odebrány.

Zvažte například následující duplicitní ingress `staging` `production` prostředky `www.contoso.com`definované obory názvů a pro:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

I přes dva příchozí přenosy prostředků náročné provoz pro `www.contoso.com` směrdo příslušných kubernetes obory názvů, pouze jeden back-end může obsluhovat provoz. AGIC by vytvořit konfiguraci na základě "kdo dřív přijde, je dřív na řadě" pro jeden z prostředků. Pokud dva příchozí prostředky jsou vytvořeny ve stejnou dobu, jeden dříve v abecedě bude mít přednost. Z výše uvedeného příkladu budeme moci `production` pouze vytvořit nastavení pro příchozí přenos dat. Brána aplikací bude nakonfigurována s následujícími prostředky:

  - Posluchače:`fl-www.contoso.com-80`
  - Pravidlo směrování:`rr-www.contoso.com-80`
  - Fond back-endu:`pool-production-contoso-web-service-80-bp-80`
  - Nastavení protokolu HTTP:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda stavu:`pb-production-contoso-web-service-80-websocket-ingress`

Všimněte si, že s výjimkou *naslouchací proces* a`production` *směrovací pravidlo*, prostředky brány aplikace vytvořené obsahují název oboru názvů ( ), pro které byly vytvořeny.

Pokud jsou dva prostředky příchozího přenosu dat zavedeny do clusteru AKS v různých časových bodech, je pravděpodobné, že AGIC `namespace-B` skončí `namespace-A`ve scénáři, kde překonfiguruje aplikační bránu a přesměruje provoz z do .

Například pokud `staging` jste přidali jako první, AGIC nakonfiguruje aplikační bránu pro směrování provozu do pracovního back-endového fondu. V pozdější fázi `production` zavedení příchozího přenosu dat způsobí, že AGIC přeprogramuje `production` aplikační bránu, která spustí směrování provozu do back-endového fondu.

## <a name="restrict-access-to-namespaces"></a>Omezit přístup k oborům názvů
Ve výchozím nastavení AGIC nakonfiguruje aplikační bránu na základě komentované příchozího přenosu dat v libovolném oboru názvů. Pokud chcete omezit toto chování, máte následující možnosti:
  - omezit obory názvů tím, že explicitně definuje obory názvů AGIC by měl sledovat pomocí klíče `watchNamespace` YAML v [helm-config.yaml](#sample-helm-config-file)
  - Použití [role/rolebinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) k omezení AGIC na konkrétní obory názvů

## <a name="sample-helm-config-file"></a>Ukázkový konfigurační soubor helmy
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

