---
title: Povolit pro Application Gateway adaptér příchozího přenosu více oborů názvů
description: Tento článek poskytuje informace o tom, jak povolit podporu více oborů názvů v clusteru Kubernetes s Application Gatewaym řadičem příchozího přenosu dat.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: cffc15974bf5a016a4584f5c5f3dcc8a185c9824
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397327"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Povolení podpory více oborů názvů v clusteru AKS s Application Gatewaym řadičem příchozího přenosu dat

## <a name="motivation"></a>Motivace
Kubernetes [obory názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) umožňují rozdělit cluster Kubernetes a přidělit podskupinům většího týmu. Tyto subtým pak mohou nasadit a spravovat infrastrukturu s přesnější kontrolou prostředků, zabezpečení, konfigurace atd. Kubernetes umožňuje nezávisle definovat jeden nebo více prostředků příchozího přenosu dat v rámci každého oboru názvů.

Od verze 0,7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) může ingestovat události z a sledovat více oborů názvů. Pokud se správce AKS rozhodne použít [App Gateway](https://azure.microsoft.com/services/application-gateway/) jako příchozí, budou všechny obory názvů používat stejnou instanci Application Gateway. Jedna instalace řadiče příchozího přenosu dat bude monitorovat dostupné obory názvů a nakonfiguruje Application Gateway, ke kterým je přidružená.

Verze 0,7 AGIC bude nadále výhradně sledovat `default` obor názvů, pokud to není explicitně změněno na jeden nebo více různých oborů názvů v konfiguraci Helm (viz část níže).

## <a name="enable-multiple-namespace-support"></a>Povolení podpory více oborů názvů
Povolení podpory více oborů názvů:
1. Upravte soubor [Helm-config. yaml](#sample-helm-config-file) jedním z následujících způsobů:
   - odstraní `watchNamespace` klíč úplně z [Helm-config. yaml](#sample-helm-config-file) -AGIC bude sledovat všechny obory názvů.
   - Nastavte `watchNamespace` na prázdný řetězec – AGIC bude sledovat všechny obory názvů.
   - Přidání více oborů názvů oddělených čárkou ( `watchNamespace: default,secondNamespace` ) – AGIC budou tyto obory názvů dodržovat výhradně.
2. použít změny šablony Helm s: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Po nasazení s možností sledovat více oborů názvů bude AGIC:
  - Vypsat prostředky příchozího přenosu ze všech přístupných oborů názvů
  - filtrovat na prostředky příchozího přenosu poznámek `kubernetes.io/ingress.class: azure/application-gateway`
  - Vytvoření kombinované [Application Gateway konfigurace](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - použít konfiguraci na přidružený Application Gateway přes [ARM](../azure-resource-manager/management/overview.md)

## <a name="conflicting-configurations"></a>Konfliktní konfigurace
Několik [prostředků](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) příchozího přenosu dat by mohlo dát AGIC pokyn, aby vytvořila konfliktní konfigurace pro jednu Application Gateway. (Dvě příchozí přenosy vyžádají stejnou doménu pro instanci.)

V horní části hierarchie – **naslouchací procesy** (IP adresa, port a hostitel) a **pravidla směrování** (naslouchací proces vazby, fond back-endu a nastavení http) se dají vytvořit a sdílet více obory názvů/příchozími přenosy.

Na dalších cestách, fondech back-endu, nastavení HTTP a certifikáty TLS může vytvořit jenom jeden obor názvů a duplikáty se odeberou.

Představte si třeba následující duplicitní obory názvů příchozích prostředků příchozího přenosu dat `staging` a `production` pro `www.contoso.com` :

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

I když dva prostředky příchozího přenosu dat pro `www.contoso.com` přesměrování na příslušné obory názvů Kubernetes, může provoz obsluhovat jenom jeden back-end. AGIC by pro jeden z prostředků vytvořila konfiguraci podle "prvního přidávaného" prvního přidávaného ". Pokud jsou dva příchozí prostředky vytvářeny současně, bude mít přednost ta, která byla dříve v abecedě. Z výše uvedeného příkladu budeme moct vytvořit nastavení pro příchozí přenos dat `production` . Application Gateway budou konfigurovány s následujícími prostředky:

  - Služby `fl-www.contoso.com-80`
  - Pravidlo směrování: `rr-www.contoso.com-80`
  - Back-end fond: `pool-production-contoso-web-service-80-bp-80`
  - Nastavení HTTP: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Sonda stavu: `pb-production-contoso-web-service-80-websocket-ingress`

Všimněte si, že kromě *pravidla* *naslouchacího procesu* a směrování vytvářené Application Gateway prostředky zahrnují název oboru názvů ( `production` ), pro který byly vytvořeny.

Pokud jsou tyto dva prostředky příchozího přenosu do clusteru AKS v různých okamžicích v čase, je možné, že AGIC skončí v situaci, kdy překonfiguruje Application Gateway a přesměruje provoz z `namespace-B` na `namespace-A` .

Pokud jste například přidali jako `staging` první, AGIC nakonfiguruje Application Gateway pro směrování provozu do přípravného back-endu. V pozdější fázi zavedení `production` příchozího přenosu dat Application Gateway do back-endu AGIC způsobí, že se služba restartuje. tím se zahájí směrování provozu do `production` back-endového fondu.

## <a name="restrict-access-to-namespaces"></a>Omezení přístupu k oborům názvů
Ve výchozím nastavení AGIC nakonfiguruje Application Gateway na základě poznámení s poznámkami v rámci libovolného oboru názvů. Chcete-li toto chování omezit, máte následující možnosti:
  - Omezte obory názvů tak, že explicitním definováním oborů názvů AGIC by měl sledovat `watchNamespace` klíč YAML v [Helm-config. yaml](#sample-helm-config-file)
  - použití [role/RoleBinding](../aks/azure-ad-rbac.md) k omezení AGIC na konkrétní obory názvů

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