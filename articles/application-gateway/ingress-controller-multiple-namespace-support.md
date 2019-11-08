---
title: Povolit pro Application Gateway adaptér příchozího přenosu více oborů názvů
description: Tento článek poskytuje informace o tom, jak povolit podporu více oborů názvů v clusteru Kubernetes s Application Gatewaym řadičem příchozího přenosu dat.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795562"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Povolení podpory více oborů názvů v clusteru AKS s Application Gatewaym řadičem příchozího přenosu dat

## <a name="motivation"></a>Motivační
Kubernetes [obory názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) umožňují rozdělit cluster Kubernetes a přidělit podskupinům většího týmu. Tyto subtým pak mohou nasadit a spravovat infrastrukturu s přesnější kontrolou prostředků, zabezpečení, konfigurace atd. Kubernetes umožňuje nezávisle definovat jeden nebo více prostředků příchozího přenosu dat v rámci každého oboru názvů.

Od verze 0,7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) může ingestovat události z a sledovat více oborů názvů. Pokud se správce AKS rozhodne použít [App Gateway](https://azure.microsoft.com/services/application-gateway/) jako příchozí, budou všechny obory názvů používat stejnou instanci Application Gateway. Jedna instalace řadiče příchozího přenosu dat bude monitorovat dostupné obory názvů a nakonfiguruje Application Gateway, ke kterým je přidružená.

Verze 0,7 AGIC bude nadále výhradně sledovat obor názvů `default`, pokud není explicitně změněno na jeden nebo více různých oborů názvů v konfiguraci Helm (viz část níže).

## <a name="enable-multiple-namespace-support"></a>Povolit podporu více oborů názvů
Povolení podpory více oborů názvů:
1. Upravte soubor [Helm-config. yaml](#sample-helm-config-file) jedním z následujících způsobů:
   - odstraní klíč `watchNamespace` výhradně z [Helm-config. yaml](#sample-helm-config-file) -AGIC bude sledovat všechny obory názvů.
   - Nastavte `watchNamespace` na prázdný řetězec – AGIC bude sledovat všechny obory názvů.
   - Přidání více oborů názvů oddělených čárkou (`watchNamespace: default,secondNamespace`) – AGIC budou tyto obory názvů dodržovat výhradně.
2. použít změny šablony Helm s: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Po nasazení s možností sledovat více oborů názvů bude AGIC:
  - Vypsat prostředky příchozího přenosu ze všech přístupných oborů názvů
  - filtrovat na prostředky příchozího přenosu poznámek pomocí `kubernetes.io/ingress.class: azure/application-gateway`
  - Vytvoření kombinované [Application Gateway konfigurace](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - použít konfiguraci na přidružený Application Gateway přes [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Konfliktní konfigurace
Několik [prostředků](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) příchozího přenosu dat by mohlo dát AGIC pokyn, aby vytvořila konfliktní konfigurace pro jednu Application Gateway. (Dvě příchozí přenosy vyžádají stejnou doménu pro instanci.)

V horní části hierarchie – **naslouchací procesy** (IP adresa, port a hostitel) a **pravidla směrování** (naslouchací proces vazby, fond back-endu a nastavení http) se dají vytvořit a sdílet více obory názvů/příchozími přenosy.

Na dalších cestách, fondech back-endu, nastavení HTTP a certifikáty TLS může vytvořit jenom jeden obor názvů a duplikáty se odeberou.

Představte si třeba následující duplicitní prostředky pro příchozí příchozí přenos dat `staging` a `production` pro `www.contoso.com`:
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

I přes to, že dva prostředky příchozího přenosu dat náročné na `www.contoso.com` mají být směrovány na příslušné obory názvů Kubernetes, může provoz obsluhovat pouze jeden back-end. AGIC by pro jeden z prostředků vytvořila konfiguraci podle "prvního přidávaného" prvního přidávaného ". Pokud jsou dva příchozí prostředky vytvářeny současně, bude mít přednost ta, která byla dříve v abecedě. Z výše uvedeného příkladu budeme moct vytvořit nastavení pro `production` příchozí přenos dat. Application Gateway budou konfigurovány s následujícími prostředky:

  - Naslouchací proces: `fl-www.contoso.com-80`
  - Pravidlo směrování: `rr-www.contoso.com-80`
  - Back-end fond: `pool-production-contoso-web-service-80-bp-80`
  - Nastavení HTTP: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda stavu: `pb-production-contoso-web-service-80-websocket-ingress`

Všimněte si, že kromě pravidla *naslouchacího procesu* a *Směrování*se vytvořily Application Gateway prostředky, které obsahují název oboru názvů (`production`), pro který byly vytvořeny.

Pokud jsou tyto dva prostředky příchozího přenosu do clusteru AKS v různých okamžicích v čase, může AGIC končit ve scénáři, kdy překonfiguruje Application Gateway a přesměruje provoz z `namespace-B` na `namespace-A`.

Pokud jste například přidali `staging` jako první, AGIC nakonfiguruje Application Gateway pro směrování provozu do přípravného back-endu. V pozdější fázi se zavedením `production` příchozího přenosu způsobí, že AGIC znovu naprogramuje Application Gateway. tím se zahájí směrování provozu do back-endu `production`ového fondu.

## <a name="restrict-access-to-namespaces"></a>Omezení přístupu k oborům názvů
Ve výchozím nastavení AGIC nakonfiguruje Application Gateway na základě poznámení s poznámkami v rámci libovolného oboru názvů. Chcete-li toto chování omezit, máte následující možnosti:
  - Omezte obory názvů tak, že explicitním definováním oborů názvů AGIC by měl být v [Helm-config. yaml](#sample-helm-config-file) sledovány prostřednictvím klíče `watchNamespace` YAML.
  - použití [role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) k omezení AGIC na konkrétní obory názvů

## <a name="sample-helm-config-file"></a>Ukázkový konfigurační soubor Helm
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

